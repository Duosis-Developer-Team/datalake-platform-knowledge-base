# Glossary and legacy product names

## Current platform name

- **Datalake Platform** — the umbrella product for this monorepo, per `.cursor/rules/PROJE-K-ML-VE-M-MAR-BA-LAM.mdc`.

## Web UI terms

- **`APP_MODE=mock`** — Environment flag read in `datalake-platform-webui-mock` (`src/services/api_client.py`, `src/utils/app_mode.py`). When set, the UI uses **`mock_client`** and **`mock_data`** instead of HTTP calls to the three FastAPI services. Not used in `Datalake-Platform-GUI`.

## Module shorthand

| Name in docs/code | Folder | Meaning |
|-------------------|--------|---------|
| Core / datalake | `datalake/` | Ingestion, NiFi, Datalake DB, Grafana |
| GUI | `Datalake-Platform-GUI/` | Dash + FastAPI microservices |
| HMDL / project-zabake | `project-zabake/` | Zabbix–NetBox–Datalake automation |
| Web UI mock | `datalake-platform-webui-mock/` | Dash UI with **`APP_MODE=mock`**, **`mock_data`**, optional full stack; **public demo** profile uses **one service** ([`docker-compose.mock.yml`](../../datalake-platform-webui-mock/docker-compose.mock.yml)). See [[05-WebUI-GUI-vs-Mock]]. |

## Legacy names in `raw/` (ORION-era)

Inbound notes and legacy planning documents live under `raw/`. ORION-era markdown and related assets are archived under [raw/archive/legacy-product-naming/](../raw/archive/legacy-product-naming/) (see [README](../raw/README.md)). They are **historical reference**, not the source of truth for the running system.

| Legacy term | Typical meaning in old docs | Current mapping |
|---------------|------------------------------|-----------------|
| ORION Platform | AI-native ops/analytics umbrella (vision PRD) | **Datalake Platform** — implement per actual modules above |
| HMDL | Host Metadata-Driven Lifecycle, GitOps-style infra | **`project-zabake`** (Ansible/AWX, Zabbix, NetBox) |
| Datalake & MLOps (as a named module) | Vision: pipelines, central store, MLOps | **`datalake`** core + operational reality in repo docs (KubeFlow etc. may be aspirational in PRD) |
| D3M (Data Driven Decision Module) | Natural-language analytics UI | Closest live counterpart: **[[02-Module-Platform-GUI]]** analytics/dashboards (not a 1:1 match to the PRD) |
| ORION Copilot | AI assistant for platform ops | Not a separate repo in this monorepo; treat as **future / vision** unless a service is added |

## How to use raw notes

1. Prefer **[[00-Platform-Overview]]** and module pages for “what we run today.”
2. Use `raw/` for background and decisions that still matter; file issues or ADRs if a legacy requirement becomes active work.
3. When integrating a raw idea into wiki, add Obsidian links (`[[Page Name]]`) and point to the archived file path if moved under `raw/archive/`.

## See also

- [[00-Platform-Overview]]
- [[01-Module-Datalake-Core]]
- [[02-Module-Platform-GUI]]
- [[03-Module-Project-Zabake-HMDL]]
- [[04-Module-WebUI-Mock]]
- [[05-WebUI-GUI-vs-Mock]]
