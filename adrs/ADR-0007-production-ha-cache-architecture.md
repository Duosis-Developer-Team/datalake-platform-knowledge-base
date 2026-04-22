# ADR-0007: Production HA and Cache Architecture for Datalake-Platform-GUI

**Date:** 2026-04-23  
**Status:** Accepted  
**Deciders:** Platform team  
**Supersedes:** (none — first production architecture ADR for this module)  
**Related:** ADR-0004 (admin-api separation), ADR-0005 (OpenTelemetry instrumentation)

---

## Context

The Datalake-Platform-GUI is a Dash-based frontend backed by three FastAPI microservices (customer-api, datacenter-api, query-api) and an admin-api. PostgreSQL provides metrics and inventory data; Redis is used for caching.

During development with a single customer (`WARMED_CUSTOMERS=Boyner`), several problems were observed and documented:

1. **Slow customer page loads** — `_customer_content` in `src/pages/customer_view.py` makes 4 sequential blocking HTTP calls. Each call may take 1-8 s on a cold cache, making total TTFB 4-32 s.

2. **Cache TTL race condition** — `CUSTOMER_DATA_CACHE_TTL_SECONDS = 900` and `REFRESH_INTERVAL_MINUTES = 15` are identical. An entry can expire during a refresh window, causing a cache miss and a live DB query mid-request. This violates the intended "stale until replaced" semantic documented in `docs/CACHE_STRATEGY_COMPARISON.md §4`.

3. **In-process singleflight is not cluster-safe** — `cache_run_singleflight` uses `threading.Event` per process. With N replicas, N concurrent cache misses each trigger a separate DB query (cache stampede).

4. **Redis is a single point of failure** — `k8s/redis/deployment.yaml` runs 1 replica as a `Deployment` without persistence, password, or TLS. Redis restart loses all cached data and causes a cold start.

5. **Warm cache is per-pod and startup-blocking** — `start_scheduler` calls `warm_cache()` synchronously before the scheduler starts. With 100+ customers × 4 time ranges, this blocks the pod for minutes on first boot, defeating rolling-update zero-downtime goals.

6. **Gunicorn under-resourced** — `--workers 1 --threads 4` means only 4 concurrent Dash callback threads per pod. Long customer renders (65 KB `customer_view.py`) block all other requests.

7. **No browser caching or compression** — Static assets and API JSON responses are served without `Cache-Control` headers or gzip compression.

---

## Decision

We adopt the following architectural decisions for production deployment. Implementation is phased across six feature branches (Faz 1-6); see [PROD_ARCHITECTURE.md](../../Datalake-Platform-GUI/docs/PROD_ARCHITECTURE.md) for the rollout sequence.

---

### Decision 1: Redis Sentinel over Redis Cluster

**Options considered:**

| Option | Pros | Cons |
|--------|------|------|
| Redis single instance (current) | Simple | SPOF; no persistence |
| Redis Cluster | Horizontal scale | Requires key hash tags; prohibits multi-key ops; complex for <1 GB data |
| **Redis Sentinel (chosen)** | HA failover; single shard; simple key space | One logical DB; needs key prefix strategy |
| Managed Redis (cloud) | Zero-ops | Not applicable (on-prem) |

**Rationale:** The total Redis working set for 100+ customers is ~161 MB (400 customer_assets keys × 150 KB + 400 last_good keys + s3 keys + dc keys). A single Redis instance easily handles this. Cluster's main benefit — horizontal scale across shards — is unnecessary. Sentinel provides automatic master failover (<30 s) with no application key-space changes, making it the right complexity/capability trade-off.

**Configuration:**
- 1 master StatefulSet + 2 replica StatefulSets + 3 sentinel pods
- `maxmemory 2gb`, `maxmemory-policy allkeys-lru`
- AOF `everysec` + RDB snapshot every 15 minutes
- `requirepass` + `masterauth` via Kubernetes Secret
- mTLS between master, replicas, and sentinels
- Application connects via `redis.sentinel.Sentinel` client (Python redis library)

---

### Decision 2: TTL = 3600 s (4× refresh interval)

**Problem:** TTL = 900 s = refresh interval → keys can expire between refresh cycles.

**Decision:** Set `cache_ttl_seconds = 3600` (1 hour) for all customer data keys. The scheduler refresh interval remains 15 minutes (900 s). Ratio = 4×.

**Effect:** Each key receives 4 scheduled write opportunities before it can expire. Even if two consecutive refreshes fail (DB timeout, network blip), the key serves stale data for up to 1 hour. This correctly implements the "old data until new data" pillar from the cache strategy document.

**Files affected:**
- `services/customer-api/app/config.py`: `cache_ttl_seconds: int = 3600`
- `services/customer-api/app/services/customer_service.py`: `CUSTOMER_DATA_CACHE_TTL_SECONDS = 3600`, `CLUSTER_ARCH_MAP_TTL_SECONDS = 3600`
- Same changes in `services/datacenter-api/app/` equivalents

---

### Decision 3: last_good shadow key for stale-serve fallback

**Problem:** on DB outage, both primary key and memory fallback miss → 503 response shown to user.

**Decision:** on every successful `cache_set`, also write `{key}:last_good` with TTL = `cache_ttl_seconds * 2` (7 200 s). On `QueryTimeoutError` or DB failure, check `{key}:last_good` before returning a 503.

**Response header:** Add `X-Cache: stale` when serving from `last_good` so monitoring and users can detect staleness.

**Effect:** Users see slightly outdated data (up to 2 hours old) rather than an error page during temporary DB outages. This is the correct UX trade-off for an operational dashboard.

---

### Decision 4: Redis-based distributed lock replaces threading.Event singleflight

**Problem:** `threading.Event` in `cache_run_singleflight` only deduplicates within one process. With 3 replicas, a cache miss causes 3 simultaneous DB queries.

**Decision:** Replace with a Redis `SET NX EX` lock. Only the pod that acquires the lock runs the factory function; others poll for up to 25 s waiting for the cache to be populated, then fall back to running the factory independently (avoiding starvation).

**Key design:** lock key = `lock:{cache_key}`, value = unique pod UUID (prevents a pod from releasing another pod's lock). Lock release uses a Lua script for atomicity.

**Retention of in-process singleflight:** Keep `threading.Event` as a secondary guard within each process to prevent per-thread DB queries on the same pod. The distributed lock operates at the cluster level; the in-process guard prevents intra-pod stampede.

---

### Decision 5: Cache warming via Kubernetes CronJob, not pod startup

**Problem:** `warm_cache()` at pod startup blocks the pod for minutes on first boot. N replicas on scale-out = N × customer_count simultaneous DB queries.

**Decision:** Disable `warm_cache()` call in `start_scheduler`. Add a Kubernetes CronJob (`*/15 * * * *`) that calls `POST /api/v1/internal/warm-cache` on the customer-api service. The warm endpoint is authenticated via a pre-shared token (`WARMER_TOKEN` in a K8s Secret).

**Pod startup behaviour after this change:**
- Pod starts, connects to Redis (already warm), serves requests immediately using Redis-cached data.
- If Redis data is absent (first cluster boot or Redis restart), requests trigger the distributed lock (Decision 4); first-request cold queries are serialised, not stampeded.
- `last_good` keys provide fallback for up to 2 hours even if warming CronJob fails once.

**`WARMED_CUSTOMERS` setting:** Remove the `WARMED_CUSTOMERS=Boyner` restriction. Set to empty string so `_load_customer_names_from_db()` loads all tenant names. The CronJob warms all customers using `ThreadPoolExecutor(max_workers=4)`.

---

### Decision 6: Frontend parallel API fetches

**Problem:** `_customer_content` in `customer_view.py` makes 4 sequential HTTP calls. Total latency = sum of all 4 durations.

**Decision:** Replace sequential calls with `concurrent.futures.ThreadPoolExecutor(max_workers=4)` to run all 4 API calls in parallel. This is the correct approach for synchronous Gunicorn gthread workers (no event loop).

**Expected gain:** TTFB p95 drops from ~4-8 s to ~1-2 s for warm cache (limited by slowest call). For cold cache: ~3-4× improvement.

**Additional Dash optimisations (tracked in Faz 2):**
- Tab-lazy loading: Summary tab renders first; other tabs load on click via separate callback.
- Export lazy-load: `_build_customer_export_sheets` moves from render path to export button callbacks.
- `dcc.Store` snapshot: avoids re-fetching API data on tab switch.
- VM table pagination: replace `html.Table` with `dash_table.DataTable` to reduce DOM size.

---

### Decision 7: Gunicorn worker increase

**Problem:** `--workers 1 --threads 4` = 4 concurrent request handlers per pod. Long callbacks block all threads.

**Decision:** `--workers 4 --threads 8 --worker-class gthread` per pod. Requires CPU limit raised to ≥ 2 vCPU for the frontend pod.

This quadruples the concurrent callback capacity without requiring async Dash (which is not supported by `gthread`).

---

### Decision 8: Browser cache and compression via NGINX Ingress

**Decision:** Configure NGINX Ingress with:
- `Cache-Control: public, max-age=31536000, immutable` for `/_dash-component-suites/` (versioned Plotly/Mantine bundles)
- `Cache-Control: private, max-age=30, stale-while-revalidate=120` for API JSON responses
- Gzip on `application/json text/css application/javascript` (reduces 100-150 KB JSON payloads to 15-25 KB)

**Rationale:** Zero code change, high impact. Eliminates repeated Plotly (~3 MB) downloads per session and reduces perceived API latency via browser-side stale-while-revalidate.

---

### Decision 9: Python 3.11 alignment

**Problem:** Root `Dockerfile` uses Python 3.10; microservices use 3.11. Mixed runtime versions complicate dependency management and miss 3.11 performance improvements.

**Decision:** Align root `Dockerfile` to Python 3.11. This is a low-risk change (Dash, Gunicorn, httpx are all 3.11-compatible). Track as part of Faz 4 (infrastructure changes).

---

## Consequences

**Positive:**
- Customer page TTFB p95 drops from 4-32 s to <800 ms (warm) / <2 500 ms (cold).
- Redis outage no longer causes total cache loss — `last_good` keys serve stale data for 2 h.
- 100+ customer warm cache is maintainable without per-pod startup overhead.
- Rolling deploys achieve zero downtime (pods start warm from Redis, not from DB).
- Cache stampede under load eliminated by distributed lock.

**Negative / trade-offs:**
- Redis Sentinel adds operational complexity (3 sentinel pods, Lua scripts, mTLS).
- `WARMED_CUSTOMERS` control is removed; all DB tenants are warmed. If the tenant table is large, CronJob duration increases. Mitigation: parallelise with `ThreadPoolExecutor`.
- Distributed lock introduces a 200 ms poll delay for follower pods on a cache miss (versus immediate DB query). This is acceptable since warm-cache hits dominate.
- Gunicorn `workers=4` increases pod memory from ~256 MB to ~512 MB per frontend pod. Adjust K8s limits accordingly.

---

## Implementation checklist (cross-reference with roadmap)

| Faz | Branch | Decisions covered |
|-----|--------|------------------|
| 1 | `feature/cache-ttl-hotfix` | 2, 3, 4, 5 (warm CronJob stub) |
| 2 | `feature/frontend-parallel-fetch` | 6, export lazy |
| 3 | `feature/redis-sentinel` | 1 |
| 4 | `feature/scale-out-ha` | 7, 9, PDB, HPA, topology spread |
| 5 | `feature/db-tuning` | pg_trgm, PgBouncer, statement_timeout |
| 6 | `feature/loadtest-slo` | 8, SLO validation |

---

## References

- [PROD_ARCHITECTURE.md](../../Datalake-Platform-GUI/docs/PROD_ARCHITECTURE.md)
- [FRONTEND_PERFORMANCE.md](../../Datalake-Platform-GUI/docs/FRONTEND_PERFORMANCE.md)
- [CACHE_STRATEGY_COMPARISON.md](../../Datalake-Platform-GUI/docs/CACHE_STRATEGY_COMPARISON.md)
- [TOPOLOGY_AND_SETUP.md](../../Datalake-Platform-GUI/docs/TOPOLOGY_AND_SETUP.md)
- `services/customer-api/app/core/cache_backend.py` — current singleflight implementation
- `services/customer-api/app/services/customer_service.py` — TTL constants, warm_cache, scheduler
- `src/pages/customer_view.py:1214-1228` — sequential fetch pattern
- `k8s/redis/deployment.yaml` — current single-replica Redis
