# ADR-0003: HMDL (project-zabake) automation standards

- **Status**: Accepted
- **Date**: 2026-04-11

## Context

**HMDL** (Host Metadata-Driven Lifecycle), implemented under `project-zabake/`, integrates **Zabbix**, **NetBox (Loki)**, and the broader **Datalake** ecosystem using Ansible and supporting Python. Without agreed defaults, teams might diverge on where configuration lives, how secrets are injected, and which integration paths are supported — increasing operational risk and doc drift.

This ADR records **platform-level principles** for HMDL. It does not replace module-level playbooks or `docs/`; it aligns with [ADR-0002](./ADR-0002-wiki-authoritative-docs-in-module-repos.md) (wiki orients; module repos stay authoritative for procedure detail).

## Decision

1. **Orchestration**: HMDL automation is **Ansible-first**. Production-style execution is expected via **AWX / Ansible Automation Platform** (schedules, credentials, audit trails) or controlled `ansible-playbook` runs — not as an undocumented one-off script path.
2. **Configuration as data**: Behavior that varies by customer or environment must be expressed in **versioned YAML** (for example `mappings/*.yml`, role `defaults/`) rather than hard-coded in playbooks. Business mapping belongs in repo config, not in AWX extra vars for static taxonomy.
3. **Secrets**: NetBox tokens, Zabbix credentials, and SMTP secrets are supplied via **AWX credentials**, **Ansible Vault**, or CI secret stores — never committed to git.
4. **Supported extension points**: New features target **`zabbix-netbox/`** and **`zabbix-monitoring/`**. The **`legacy/`** tree is for continuity of older workflows (platform sync file handoff, CSV import), not for new product features unless explicitly replatformed.
5. **Monitoring convention**: The **preferred** connectivity automation is **tag-based** (items tagged in Zabbix, e.g. default tag `connection status` per module docs). Template-only deep coupling in the monitoring role is **legacy** and should not be extended.
6. **Cross-repo contracts**: If HMDL exposes HTTP APIs or payloads consumed by the GUI or mock, follow the platform rule: update **Datalake-Platform-GUI** and **datalake-platform-webui-mock** in tandem (see wiki [00-Platform-Overview](../wiki/00-Platform-Overview.md)).

## Consequences

- Onboarding can point to [03-Module-Project-Zabake-HMDL](../wiki/03-Module-Project-Zabake-HMDL.md) for a single map of playbooks and standards; detailed steps remain in `project-zabake/**/docs/`.
- Reviews can reject changes that add long-lived secrets, bypass Ansible for maintainable sync paths, or grow `legacy/` without a migration plan.
- If HMDL adds GitHub Actions or other CI in the future, this ADR does not forbid it; update this ADR or add a follow-up ADR if the operational model materially changes.
