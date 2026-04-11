# Module: Datalake-Platform-GUI

## Role

**Presentation and API gateway to the UI**: Dash application (`app.py`) calls three **FastAPI** services — **datacenter-api**, **customer-api**, **query-api** — over HTTP. **PostgreSQL** holds metrics and inventory. **Redis** caches datacenter and customer API responses (graceful degradation if Redis is unavailable). **External HTTP API** supplies SLA metrics for the datacenter service.

This module is the path for **operational visualization** of the datalake-backed environment (plus optional **authentication**, RBAC, and JWT forwarding to APIs). It is **not** the public demo-only stack; for that, see [[05-WebUI-GUI-vs-Mock]].

## Authoritative docs

- [docs/TOPOLOGY_AND_SETUP.md](../../Datalake-Platform-GUI/docs/TOPOLOGY_AND_SETUP.md) — topology, routes under `/api/v1`, environment variables, probes
- [docs/PROJECT_STANDARDS.md](../../Datalake-Platform-GUI/docs/PROJECT_STANDARDS.md) — UI/API standards (e.g. S3 dashboards)
- [k8s/ingress.yaml](../../Datalake-Platform-GUI/k8s/ingress.yaml) — example ingress paths for APIs

## Cross-repo synchronization

When backend endpoints or contracts change in [[01-Module-Datalake-Core]] or [[03-Module-Project-Zabake-HMDL]], update:

- GUI clients and types under `Datalake-Platform-GUI/src/`
- Mock implementations under `datalake-platform-webui-mock/` (see [[04-Module-WebUI-Mock]])

See [[00-Platform-Overview]] — section *Cross-repo API, GUI, and mock synchronization*.

## See also

- [[00-Platform-Overview]]
- [[04-Module-WebUI-Mock]]
- [[05-WebUI-GUI-vs-Mock]]
