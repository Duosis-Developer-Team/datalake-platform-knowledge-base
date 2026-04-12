# Datalake Platform — Overview

This wiki is the **navigation layer** for the Datalake Platform monorepo. Authoritative technical detail lives in each module repository under `datalake/`, `Datalake-Platform-GUI/`, `project-zabake/`, and `datalake-platform-webui-mock/`. Prefer linking there instead of duplicating content.

## Map of content

- [ADRs (decision log)](../adrs/README.md) — accepted decisions about documentation and KB layout
- [[01-Module-Datalake-Core]] — ingestion, NiFi, central database, Grafana
- [[02-Module-Platform-GUI]] — Dash UI and FastAPI microservices
- [[03-Module-Project-Zabake-HMDL]] — Zabbix, NetBox, Ansible/AWX automation
- [[04-Module-WebUI-Mock]] — mock APIs and UI development profile
- [[05-WebUI-GUI-vs-Mock]] — customer demo (mock, single-service) vs datalake-connected GUI (live data and external APIs)
- [[06-WebUI-Data-Lineage]] — WebUI twin repos: routes, `api_client`, APIs, and primary PostgreSQL tables
- [[99-Glossary-And-Legacy-Names]] — product naming (ORION-era docs vs current modules)

## Platform shape (summary)

- **Multi-tenant, modular** delivery: modules can be toggled per customer via Helm `values.yaml` and feature flags (see project Cursor rules).
- **Orchestration**: Kubernetes-oriented deployments; GitOps-style configuration is the operational target (umbrella chart expectation per architecture rules).
- **Modules** (canonical four): `datalake` (core), `Datalake-Platform-GUI` (frontend + APIs), `project-zabake` (Zabbix and related integrations), `datalake-platform-webui-mock` (mock APIs for UI work).

When Helm charts or sample `values.yaml` land in this repo, add a link from this page to that path.

## Cross-repo API, GUI, and mock synchronization

When you add or change an API in **`datalake` (core)** or **`project-zabake`**:

1. Check whether **`Datalake-Platform-GUI`** consumption models (types, clients, pages) need updates.
2. Update **`datalake-platform-webui-mock`** mock data and routes to stay aligned with the real contract.

The GUI should respect **feature flags** and **module permissions** from backend JSON where applicable. Workspace rule: `.cursor/rules/APRAZ-REPOSITORY-CROSS-REPO-L-K-LER.mdc`.

Related wiki pages: [[02-Module-Platform-GUI]], [[04-Module-WebUI-Mock]], [[05-WebUI-GUI-vs-Mock]], [[03-Module-Project-Zabake-HMDL]].
