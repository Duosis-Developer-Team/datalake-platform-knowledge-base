# ADR-0005: OpenTelemetry instrumentation (GUI stack)

- **Status**: Accepted
- **Date**: 2026-04-18

## Context

Operators need **application observability** across the Datalake Platform GUI stack: the Dash/Flask app (`datalake-webui`) and the FastAPI microservices (**datacenter-api**, **customer-api**, **query-api**, **admin-api**). Traces must link **browser-facing HTTP**, **outbound HTTP to APIs**, **Redis cache** behavior, and **PostgreSQL** queries. Telemetry is exported to an **external OpenTelemetry Collector** via **OTLP gRPC** (not hosted in this repository).

## Decision

1. **Enable telemetry with environment flags**  
   - `OTEL_ENABLED=true` turns on SDK initialization; when unset/false, instrumentation is skipped (no-op for production overhead when disabled).

2. **Service naming**  
   - `datalake-webui` for the Dash app; per-service names for APIs (`datacenter-api`, `customer-api`, `query-api`, `admin-api`) via `OTEL_SERVICE_NAME` (defaults in code match these names).

3. **Export**  
   - `OTEL_EXPORTER_OTLP_ENDPOINT` (host:port or URL) and optional `OTEL_EXPORTER_OTLP_INSECURE` (default `true` for plaintext gRPC to internal collectors).

4. **Auto-instrumentation**  
   - **Web UI**: Flask, httpx, requests, psycopg2 (auth DB).  
   - **APIs**: FastAPI, httpx, requests, psycopg2; **Redis** on datacenter-api and customer-api only.

5. **Custom spans**  
   - Auth: login/logout spans; Flask `after_request` adds `enduser.id` / `enduser.name` on the active server span.  
   - Dash: `dash.callback.render_main_content` (and helper decorator pattern for other callbacks).  
   - Cache: `cache.get` and `cache.singleflight` spans with attributes `cache.hit`, `cache.backend`, `cache.singleflight.waited` in `cache_backend.py` (datacenter-api, customer-api).

6. **Logs**  
   - Optional OTLP log export from the web UI via OpenTelemetry `LoggingHandler` when the logs SDK initializes successfully.

7. **Deployment**  
   - `docker-compose.yml` passes `OTEL_*` variables to all services; the collector itself is **out of scope** for this ADR.

## Consequences

- **Positive**: End-to-end distributed traces when upstream (httpx) propagates W3C trace context to FastAPI; cache and DB appear as child spans.  
- **Negative**: Dependency surface (OpenTelemetry packages) and need to align package versions across services.  
- **Follow-up**: If backend APIs are deployed separately, ensure the same `OTEL_*` env vars are set in Helm/Kubernetes values.

## References

- Wiki: [[02-Module-Platform-GUI]] (observability subsection).  
- Operator guide (Collector URL, `.env`, Java agent mapping, limitations): [`Datalake-Platform-GUI/docs/OTEL_COLLECTOR.md`](../../Datalake-Platform-GUI/docs/OTEL_COLLECTOR.md).  
- Code: `Datalake-Platform-GUI/src/telemetry/`, `Datalake-Platform-GUI/services/*/app/telemetry.py`, `docker-compose.yml`.
