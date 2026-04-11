# ADR-0002: Wiki as navigation layer; module repos hold authoritative technical docs

- **Status**: Accepted
- **Date**: 2026-04-11

## Context

The platform spans multiple top-level modules (`datalake/`, `Datalake-Platform-GUI/`, `project-zabake/`, `datalake-platform-webui-mock/`). Duplicating architecture and API details in the Obsidian wiki would drift quickly and conflict with the source of truth in code and module-level `docs/`.

## Decision

- **`datalake-platform-knowledge-base/wiki/`** provides **orientation, maps of content, glossary, and cross-links** — not full duplicates of module documentation.
- **Authoritative** technical documentation remains in each module (e.g. `datalake/docs/`, `Datalake-Platform-GUI/docs/`).
- When behavior changes, update the **module docs first**, then adjust wiki links or short summaries if needed.

## Consequences

- Lower maintenance burden and fewer contradictions.
- Wiki pages must prefer **relative links** into sibling module folders (e.g. `../../datalake/README.md`).
- New features should add or update module `docs/`; the wiki gains a pointer from [[00-Platform-Overview]] or the relevant module page.
