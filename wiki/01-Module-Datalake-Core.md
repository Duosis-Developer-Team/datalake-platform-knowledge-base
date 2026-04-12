# Module: datalake (core)

## Role

Central **data plane**: collectors across DCs, NiFi pipelines, queue PostgreSQL at remote sites, main NiFi and Datalake DB at the hub, Grafana dashboards.

## High-level flow

`Collector scripts → Remote NiFi → Queue (PostgreSQL) → Main NiFi → Datalake DB → Grafana`

## Authoritative docs (read these first)

- [datalake/README.md](../../datalake/README.md) — purpose, topology summary, outputs
- [datalake/docs/architecture-overview.md](../../datalake/docs/architecture-overview.md) — customer environment architecture
- [datalake/docs/docs-plan.md](../../datalake/docs/docs-plan.md) — documentation structure and maintenance rules

## Collector catalog (standards and cross-module)

Cross-module view: compliance vs [collector template](../../datalake/docs/development-templates/collector_discovery_template.md), GUI table mapping, and gaps. Start at [[datalake-collectors/00-Index|datalake-collectors index]].

## Cross-repo note

API or schema changes here may require updates in [[02-Module-Platform-GUI]] and [[04-Module-WebUI-Mock]]. See [[00-Platform-Overview#Cross-repo API, GUI, and mock synchronization]]. For which GUI screens consume which tables (live path), see [[06-WebUI-Data-Lineage]].

## See also

- [[00-Platform-Overview]]
- [[06-WebUI-Data-Lineage]]
- [[99-Glossary-And-Legacy-Names]]
