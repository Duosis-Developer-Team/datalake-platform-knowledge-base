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
