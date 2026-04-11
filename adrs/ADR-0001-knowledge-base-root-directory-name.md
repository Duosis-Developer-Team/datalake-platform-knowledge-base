# ADR-0001: Knowledge base root directory name

- **Status**: Accepted
- **Date**: 2026-04-11

## Context

Cursor workspace rules referred to a `knowledge-base/` directory at the repository root, while the vault actually lived under `datalake-platform-knowledge-base/`. Renaming the folder to `knowledge-base/` was attempted but blocked by the environment (access denied), so an alternative was required to avoid split-brain paths for humans and automation.

## Decision

The **canonical** knowledge base root directory name is **`datalake-platform-knowledge-base/`** at the monorepo root. The Cursor rule `.cursor/rules/OBSIDIAN-KNOWLEDGE-BASE.mdc` is updated to reference this path for `wiki/`, `adrs/`, and `raw/`.

If the folder is ever renamed (e.g. to `knowledge-base/`), update that rule and add a new ADR that supersedes this one.

## Consequences

- Agents and developers must use `datalake-platform-knowledge-base/` in instructions and links.
- Documentation that assumed `knowledge-base/` must be migrated to the canonical name.
