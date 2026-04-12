# Module: datalake-platform-webui-mock

## Role

**Mock stack** for **public customer demos** and UI work **without** full backend dependencies: **`APP_MODE=mock`**, static **`mock_data`**, and a **minimal one-service** deploy ([`docker-compose.mock.yml`](../../datalake-platform-webui-mock/docker-compose.mock.yml), [`k8s-mock/`](../../datalake-platform-webui-mock/k8s-mock/)) — no PostgreSQL, Redis, or three APIs required for that profile. Parallel layout to the GUI repo (`services/`, `src/`). Keeps mock responses aligned with real API contracts used by `Datalake-Platform-GUI`.

Full comparison of intent and code-level differences: [[05-WebUI-GUI-vs-Mock]].

## Where to look

- [docker-compose.mock.yml](../../datalake-platform-webui-mock/docker-compose.mock.yml) — mock-oriented compose
- [docs/](../../datalake-platform-webui-mock/docs/) — project standards and notes where present
- [tests/](../../datalake-platform-webui-mock/tests/) — contract and behavior tests for mocks

## Settings (mock parity)

Mock exposes the same URL structure as the GUI for demos:

- `/settings` — dashboard KPIs + section cards (data: `src/services/mock_data/settings_data.py`)
- `/settings/iam/*` — users, teams, roles, permissions, auth, audit
- `/settings/integrations/*` — overview, LDAP, AuraNotify (static SLA table)

Routing: `app.py` delegates `pathname.startswith("/settings")` to `src/pages/settings/shell.py`. Page bodies are split to mirror the GUI layout: `src/pages/settings/dashboard.py`, `src/pages/settings/iam/*.py`, `src/pages/settings/integrations/*.py`. The sidebar enables **Settings** with `href="/settings"`.

`Query Explorer` mock layout mirrors GUI styling (`src/pages/query_explorer.py` + `src/utils/ui_tokens.py`).

## Cross-repo synchronization

Whenever [[01-Module-Datalake-Core]] or [[03-Module-Project-Zabake-HMDL]] exposes a new endpoint or payload shape that the GUI uses, update mock data and tests here. Pair with [[02-Module-Platform-GUI]] client changes. See [[00-Platform-Overview]] — section *Cross-repo API, GUI, and mock synchronization*.

## See also

- [[00-Platform-Overview]]
- [[02-Module-Platform-GUI]]
- [[05-WebUI-GUI-vs-Mock]]
- [[06-WebUI-Data-Lineage]]
