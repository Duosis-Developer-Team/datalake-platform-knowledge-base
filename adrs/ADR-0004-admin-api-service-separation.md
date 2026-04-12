# ADR-0004: Admin API Service Separation

- **Status**: Accepted
- **Date**: 2026-04-12

## Context

The `app` Dash container was responsible for both **frontend rendering** (UI pages, callbacks) and **backend operations** (direct PostgreSQL access for user, role, LDAP, team, audit CRUD via `src/auth/settings_crud.py` + Flask blueprint routes at `/auth/settings/*`).

This violated the single-responsibility principle and made the frontend container heavier than necessary. Any settings CRUD bug required redeploying the entire Dash frontend. Additionally, settings data could not be consumed by future services (e.g. a separate admin CLI or mobile app) without going through the Dash/Flask layer.

The `app` service also had direct database credentials (`AUTH_DB_*`) baked into its environment, making it a wider attack surface than a pure frontend should have.

## Decision

Extract all **settings CRUD operations** into a dedicated **`admin-api`** FastAPI microservice (`services/admin-api/`), following the same pattern as `datacenter-api`, `customer-api`, and `query-api`.

| Responsibility | Before (app) | After |
|---|---|---|
| Dash UI rendering | `app` | `app` (unchanged) |
| Session / login / logout | `app` Flask blueprint | `app` Flask blueprint (unchanged) |
| User / role / permission CRUD | `app` direct DB | **`admin-api`** REST API |
| LDAP config CRUD | `app` direct DB | **`admin-api`** REST API |
| Audit log reads | `app` direct DB | **`admin-api`** REST API |

The Dash app calls `admin-api` through `src/services/admin_client.py`, which:
- Routes all calls to `ADMIN_API_URL` when the env var is set.
- Falls back to `src/auth/settings_crud.py` (direct DB) when `ADMIN_API_URL` is **not** set, preserving single-service deployments.

The `admin-api` exposes:
- `GET/POST /api/v1/users`, `/api/v1/users/{id}/roles`, `/api/v1/users/{id}/active`
- `GET /api/v1/roles`, `/api/v1/roles/{id}/permissions`, `POST /api/v1/roles/{id}/matrix`
- `GET/POST /api/v1/permissions`
- `GET/POST /api/v1/teams`
- `GET/POST /api/v1/ldap`, `/api/v1/ldap/{id}/mappings`, `DELETE /api/v1/ldap/mappings/{id}`
- `GET /api/v1/audit`
- `GET /health`, `/ready`

Authentication on `admin-api` routes is controlled by `API_AUTH_REQUIRED` (default: `false` for internal network use; enable with JWT for external exposure).

Additionally, the `roles.py` permission matrix page was refactored from an HTML form POST to a **Dash callback** approach (`dcc.Checklist` + `dcc.Store` + `dmc.Button`) to fix a `html.Input` AttributeError on Dash 2.18+.

## Consequences

### Positive
- `app` container is a **pure frontend** — no direct database credentials needed long-term (auth DB still required for session handling in this phase).
- `admin-api` is independently deployable, scalable, and testable.
- Settings CRUD can be consumed by future services (CLI tools, mobile apps, other platforms).
- OpenAPI docs available at `http://admin-api:8000/docs`.
- Rollback path: unset `ADMIN_API_URL` to revert to direct DB mode without code changes.

### Negative / Follow-up
- Session handling (login/logout) still runs in `app`. A future ADR may extract this to `admin-api` or a dedicated `auth-api`.
- `admin-api` currently uses synchronous psycopg2; migrate to asyncpg in a future iteration if throughput requires it.
- Direct DB credentials (`AUTH_DB_*`) remain in `app` env for session middleware. Clean this up once auth is fully extracted.

## References

- [[02-Module-Platform-GUI]] — updated service architecture diagram
- `Datalake-Platform-GUI/services/admin-api/` — service source
- `Datalake-Platform-GUI/src/services/admin_client.py` — HTTP client with fallback
