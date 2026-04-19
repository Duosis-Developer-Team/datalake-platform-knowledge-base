# Architecture Decision Records (ADRs)

This directory records **significant, stable decisions** about the Datalake Platform knowledge base and (when relevant) documentation strategy. ADRs are immutable history: supersede with a new ADR instead of rewriting old ones.

## Conventions

- **Filename**: `ADR-NNNN-short-title.md` (four-digit number, zero-padded).
- **Status**: Proposed | Accepted | Superseded | Deprecated
- **One decision per ADR** — if scope drifts, split a new ADR.

## Template

```markdown
# ADR-NNNN: Title

- **Status**: Accepted
- **Date**: YYYY-MM-DD

## Context

What problem or constraint triggered this decision?

## Decision

What did we decide?

## Consequences

Positive and negative outcomes, follow-up work, and links to wiki or code.
```

## Index

| ADR | Title |
|-----|--------|
| [ADR-0001](./ADR-0001-knowledge-base-root-directory-name.md) | Knowledge base root directory name |
| [ADR-0002](./ADR-0002-wiki-authoritative-docs-in-module-repos.md) | Wiki as navigation layer; module repos hold authoritative technical docs |
| [ADR-0003](./ADR-0003-hmdl-automation-standards.md) | HMDL (project-zabake) automation standards |
| [ADR-0004](./ADR-0004-admin-api-service-separation.md) | Admin API service separation — `app` frontend-only, settings CRUD in `admin-api` |
| [ADR-0005](./ADR-0005-opentelemetry-instrumentation.md) | OpenTelemetry instrumentation for `datalake-webui` and FastAPI microservices (OTLP gRPC) |
| [ADR-0006](./ADR-0006-netbox-location-hierarchy-resolution.md) | NetBox location hierarchy resolution for HMDL (`zabbix-netbox`) Zabbix sync |
