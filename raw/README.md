# Raw inbound notes

This folder holds **unstructured** inputs: meeting notes, exports, diagrams, and ideas before they are distilled into the wiki or module documentation.

## Current layout

- **`archive/legacy-product-naming/`** — Older documents that used **ORION**, **D3M**, **ORION Copilot**, and related names from prior planning. Each archived markdown file has a short header pointing to the current glossary and platform overview. Binary assets (e.g. topology JPG) are stored next to those exports.
- **`archive/Datalake-Platform-GUI-2026-04-19/`** — Snapshot of **`task/`** (feature notes) and **`legacy/`** (SPEC\*) removed from the **Datalake-Platform-GUI** repo during cleanup; not authoritative for current code.

## What to do with new raw files

1. Drop new notes here (or in a dated subfolder if volume grows).
2. When a note implies a durable decision, add or update an [ADR](../adrs/README.md) and link from the wiki.
3. Integrate stable facts into **`datalake/`**, **`Datalake-Platform-GUI/`**, **`project-zabake/`**, or **`datalake-platform-webui-mock/`** `docs/` per [ADR-0002](../adrs/ADR-0002-wiki-authoritative-docs-in-module-repos.md).

## Authoritative navigation

- Wiki map: [../wiki/00-Platform-Overview.md](../wiki/00-Platform-Overview.md)
- Glossary (legacy vs current names): [../wiki/99-Glossary-And-Legacy-Names.md](../wiki/99-Glossary-And-Legacy-Names.md)
