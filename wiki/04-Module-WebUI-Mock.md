# Module: datalake-platform-webui-mock

## Role

**Mock stack** for **public customer demos** and UI work **without** full backend dependencies: **`APP_MODE=mock`**, static **`mock_data`**, and a **minimal one-service** deploy ([`docker-compose.mock.yml`](../../datalake-platform-webui-mock/docker-compose.mock.yml), [`k8s-mock/`](../../datalake-platform-webui-mock/k8s-mock/)) — no PostgreSQL, Redis, or three APIs required for that profile. Parallel layout to the GUI repo (`services/`, `src/`). Keeps mock responses aligned with real API contracts used by `Datalake-Platform-GUI`.

Full comparison of intent and code-level differences: [[05-WebUI-GUI-vs-Mock]].

## Where to look

- [docker-compose.mock.yml](../../datalake-platform-webui-mock/docker-compose.mock.yml) — mock-oriented compose
- [docs/](../../datalake-platform-webui-mock/docs/) — project standards and notes where present
- [tests/](../../datalake-platform-webui-mock/tests/) — contract and behavior tests for mocks

## Cross-repo synchronization

Whenever [[01-Module-Datalake-Core]] or [[03-Module-Project-Zabake-HMDL]] exposes a new endpoint or payload shape that the GUI uses, update mock data and tests here. Pair with [[02-Module-Platform-GUI]] client changes. See [[00-Platform-Overview]] — section *Cross-repo API, GUI, and mock synchronization*.

## See also

- [[00-Platform-Overview]]
- [[02-Module-Platform-GUI]]
- [[05-WebUI-GUI-vs-Mock]]
- [[06-WebUI-Data-Lineage]]
