# Module: Datalake-Platform-GUI

## Role

**Presentation and API gateway to the UI**: Dash application (`app.py`) calls four **FastAPI** services — **datacenter-api**, **customer-api**, **query-api**, and **admin-api** — over HTTP. **PostgreSQL** holds metrics, inventory, and auth data. **Redis** caches datacenter and customer API responses (graceful degradation if Redis is unavailable). **External HTTP API** supplies SLA metrics for the datacenter service.

The `app` container is a **pure Dash frontend** — it only handles rendering, session management, and login/logout. All settings CRUD (users, roles, permissions, teams, LDAP, audit) is delegated to the **admin-api** microservice via `src/services/admin_client.py`.

This module is the path for **operational visualization** of the datalake-backed environment (plus optional **authentication**, RBAC, and JWT forwarding to APIs). It is **not** the public demo-only stack; for that, see [[05-WebUI-GUI-vs-Mock]].

## Authoritative docs

- [docs/PROD_ARCHITECTURE.md](../../Datalake-Platform-GUI/docs/PROD_ARCHITECTURE.md) — **Production HA topology**: Redis Sentinel, HPA/PDB, HW/SW sizing (100+ customers), SLOs, zero-downtime rollout, cache policy; see also [[ADR-0007-production-ha-cache-architecture]]
- [docs/FRONTEND_PERFORMANCE.md](../../Datalake-Platform-GUI/docs/FRONTEND_PERFORMANCE.md) — Browser cache headers, Dash parallel fetch, progressive render, VM table pagination, export lazy-load
- [docs/CACHE_STRATEGY_COMPARISON.md](../../Datalake-Platform-GUI/docs/CACHE_STRATEGY_COMPARISON.md) — Legacy vs Redis cache semantics; §4a production resolution (TTL=3600, last_good key, distributed lock, CronJob warm)
- [docs/TOPOLOGY_AND_SETUP.md](../../Datalake-Platform-GUI/docs/TOPOLOGY_AND_SETUP.md) — **Development / local** topology, routes under `/api/v1`, environment variables, probes
- [docs/OTEL_COLLECTOR.md](../../Datalake-Platform-GUI/docs/OTEL_COLLECTOR.md) — OpenTelemetry → external Collector (OTLP gRPC), `.env` / Compose, Java agent mapping
- [docs/PROJECT_STANDARDS.md](../../Datalake-Platform-GUI/docs/PROJECT_STANDARDS.md) — UI/API standards (e.g. S3 dashboards)
- [k8s/ingress.yaml](../../Datalake-Platform-GUI/k8s/ingress.yaml) — example ingress paths for APIs
- [`.env.example`](../../Datalake-Platform-GUI/.env.example) — single root template (copy to `.env`): `DB_*`, `AUTH_*`, API URLs, Redis, optional OTEL; replaces the former split `env.example` / `.env.example` pair

## Cross-repo synchronization

When backend endpoints or contracts change in [[01-Module-Datalake-Core]] or [[03-Module-Project-Zabake-HMDL]], update:

- GUI clients and types under `Datalake-Platform-GUI/src/`
- Mock implementations under `datalake-platform-webui-mock/` (see [[04-Module-WebUI-Mock]])

See [[00-Platform-Overview]] — section *Cross-repo API, GUI, and mock synchronization*.

## Settings UI (IAM + Integrations)

The Dash **Settings** area is organized as a small product shell:

- **Landing**: `/settings` — KPIs, IAM/Integrations section cards, recent audit rows, auth/build info.
- **IAM**: `/settings/iam/*` — Users, Teams, Roles (with permission matrix + `?role_id=` deep link), Permissions tree, Auth settings, Audit log.
- **Integrations**: `/settings/integrations/*` — Overview (connector grid + activity), LDAP configuration, **AuraNotify** (env-driven config + live SLA table via `auranotify_client`).

Legacy URLs such as `/settings/users` redirect to the new IAM paths; `/settings/ldap` redirects to `/settings/integrations/ldap`.

Permission codes include `page:settings_integrations` and `page:settings_auranotify` (see `src/auth/permission_catalog.py`).

Shared visual tokens live in `src/utils/ui_tokens.py` (`card_style`, `kpi_card`, `section_nav_card`, `th_left`, `th_center`, etc.).

**Settings data flow**: All settings pages import `from src.services import admin_client as settings_crud`. When `ADMIN_API_URL` is set, `admin_client` routes calls to the **admin-api** service. Without it, `admin_client` falls back to `src.auth.settings_crud` for single-service deployments.

**IAM LDAP directory import (Users page)**: With an active LDAP configuration, admins can **search Active Directory / LDAP** (`GET /api/v1/ldap/search?q=`). Matching accounts are listed **inline** on the Users card (no modal); admins select checkboxes, assign **roles** and **teams**, then **import** (`POST /api/v1/users/import-ldap`). User rows support **Edit** (profile, roles, teams) via `GET/PUT /api/v1/users/{id}` and related endpoints. **Teams** supports rename (`PUT /api/v1/teams/{id}`) and **member** CRUD (`/api/v1/teams/{id}/members`). **Roles** supports renaming non-system roles and deleting custom roles (`PUT/DELETE /api/v1/roles/{id}`). Implementation references: `services/admin-api/app/routers/{ldap,users,teams,roles}.py`, `src/pages/settings/iam/users_callbacks.py`, `ldap_ops.py`, `src/auth/ldap_service.search_directory_users` (single-service fallback).

**LDAP Settings (Integrations)**: The LDAP form includes **Test connection** (`POST /api/v1/ldap/test`) which performs a bind and a short user search (capped at 3 results) using the form fields; if `bind_password` is empty and `ldap_id` is sent, the stored encrypted password is used (same as save). In **admin-api** `ldap_ops`, LDAP connections use `get_info=NONE` (no schema fetch). Requesting a fixed list of attribute names in that mode can trigger **“attribute type not present”** on some directories; searches therefore use **`ALL_ATTRIBUTES` (`*`)** and map `sAMAccountName` / `uid` / `cn`, `displayName`, `mail` from the entry in code (DN from `entry_dn` only — never request `distinguishedName` as an attribute). The GUI’s `ldap_service` path uses the same mapping for parity. **Group → role** mapping uses a role **dropdown** (`list_roles`) with the selected `role_id` posted via a hidden field. Callbacks: `src/pages/settings/integrations/ldap_callbacks.py`.

## Customer navigation UX

- The customer flow now starts at **`/customers`** with a searchable customer card grid.
- Each customer card routes to **`/customer-view?customer=<name>`**, allowing direct deep-linking to a selected tenant.
- The GUI startup customer list is restricted to `WARMED_CUSTOMERS` intersection, so pilot-only deployments can lock the visible customer set (for example, `("Boyner",)`).

## Service Architecture (GUI stack)

```
app (Dash / Flask, port 8050)
  ├── datacenter-api  (FastAPI, port 8000)
  ├── customer-api    (FastAPI, port 8001)
  ├── query-api       (FastAPI, port 8002)
  └── admin-api       (FastAPI, port 8060)  ← IAM/LDAP/Audit CRUD
        └── auth-db   (PostgreSQL, port 5432)
```

See [[ADR-0004-admin-api-service-separation]] for the rationale behind extracting the admin API.

## OpenTelemetry (application observability)

The GUI stack can export **traces** (and optional **logs** from the web UI) to an **external OpenTelemetry Collector** using **OTLP gRPC**. See [[ADR-0005-opentelemetry-instrumentation]] and the operator guide [OTEL_COLLECTOR.md](../../Datalake-Platform-GUI/docs/OTEL_COLLECTOR.md) (`.env` examples, Compose service names, comparison to Java OpenTelemetry agent flags, sampling limitations).

- **datalake-webui**: `src/telemetry/setup.py` — Flask, httpx, requests, psycopg2; Dash callback span `dash.callback.render_main_content`; auth spans; `enduser.*` on HTTP spans via middleware.  
- **Microservices**: `services/<name>/app/telemetry.py` — FastAPI + psycopg2 (+ Redis on datacenter-api and customer-api).  
- **Cache visibility** (datacenter-api, customer-api): `app/core/cache_backend.py` adds spans `cache.get` / `cache.singleflight` with `cache.hit`, `cache.backend`, `cache.singleflight.waited`.  
- **Configuration**: set `OTEL_ENABLED=true`, `OTEL_EXPORTER_OTLP_ENDPOINT`, and per-service `OTEL_SERVICE_NAME` (see `docker-compose.yml` for fixed defaults per container). Collector configuration is **not** part of this repo.

## GUI HTTP client resilience and service cache refresh

- **`src/services/api_client.py`**: Successful microservice JSON payloads are stored in **`src/services/cache_service.py`** (in-process, bounded key space). On transport/HTTP errors, the client returns the **last successful payload** for the same logical key instead of silently returning empty placeholder structures. **AuraNotify** customer availability keeps the same TTL behaviour but **does not replace** a previously good bundle when a refresh fails.
- **`services/customer-api`**: **APScheduler** runs the same **15-minute** cache rebuild cadence as **datacenter-api** (`warm_cache` on startup, then `refresh_all_data`). Customer names for warm/refresh come from **`WARMED_CUSTOMERS`** when set, otherwise **distinct `tenant_name`** values from `discovery_netbox_inventory_device` (see `CUSTOMER_NAME_LIST` in `app/db/queries/customer.py`).
- **`services/datacenter-api`**: Scheduler also runs **S3** and **backup** cache refresh jobs on a **30-minute** interval (`refresh_s3_cache`, `refresh_backup_cache`).
- **Dash shell**: HTML / `/_dash/*` responses remain **`Cache-Control: no-store`**; fingerprinted **`/assets/*`** JS/CSS responses may use **`immutable`** long-lived caching (`app.py` `after_request`).

## Production readiness status

| Area | Status | Ref |
|------|--------|-----|
| Redis HA (Sentinel) | Planned — Faz 3 | [[ADR-0007-production-ha-cache-architecture]] |
| Cache TTL fix (900→3600 s) | Planned — Faz 1 | [CACHE_STRATEGY_COMPARISON.md §4a](../../Datalake-Platform-GUI/docs/CACHE_STRATEGY_COMPARISON.md) |
| Distributed singleflight | Planned — Faz 1 | [CACHE_STRATEGY_COMPARISON.md §4a.3](../../Datalake-Platform-GUI/docs/CACHE_STRATEGY_COMPARISON.md) |
| Cache warm CronJob | Planned — Faz 1 | [PROD_ARCHITECTURE.md §5.4](../../Datalake-Platform-GUI/docs/PROD_ARCHITECTURE.md) |
| Frontend parallel fetch | Planned — Faz 2 | [FRONTEND_PERFORMANCE.md §2.1](../../Datalake-Platform-GUI/docs/FRONTEND_PERFORMANCE.md) |
| Tab-lazy loading | Planned — Faz 2 | [FRONTEND_PERFORMANCE.md §2.2](../../Datalake-Platform-GUI/docs/FRONTEND_PERFORMANCE.md) |
| Scale out (replicas ×3, PDB, HPA) | Planned — Faz 4 | [PROD_ARCHITECTURE.md §4](../../Datalake-Platform-GUI/docs/PROD_ARCHITECTURE.md) |
| DB tuning (pg_trgm, PgBouncer, timeout) | Planned — Faz 5 | [PROD_ARCHITECTURE.md §6](../../Datalake-Platform-GUI/docs/PROD_ARCHITECTURE.md) |
| Load test + SLO validation | Planned — Faz 6 | [PROD_ARCHITECTURE.md §1](../../Datalake-Platform-GUI/docs/PROD_ARCHITECTURE.md) |

## See also

- [[00-Platform-Overview]]
- [[04-Module-WebUI-Mock]]
- [[05-WebUI-GUI-vs-Mock]]
- [[06-WebUI-Data-Lineage]]
