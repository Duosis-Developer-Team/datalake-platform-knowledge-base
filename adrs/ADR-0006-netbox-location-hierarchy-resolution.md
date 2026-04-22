# ADR-0006: NetBox location hierarchy resolution for HMDL Zabbix sync

- **Status**: Accepted
- **Date**: 2026-04-15

## Context

The `zabbix-netbox` Ansible role maps NetBox DCIM locations to Zabbix host group / `DC_ID` labels via `get_location_name`. An earlier implementation only considered the **immediate parent** once. For chains such as `DC18 → DH3 → Rack`, devices on the leaf resolved to **DH3** instead of the intended root **DC18**.

The device-fetch **location name filter** only included **direct children** of the selected location, excluding deeper descendants.

## Decision

1. **Root location name:** Walk the NetBox `dcim/locations/{id}/` record repeatedly until `parent` is `null`; return that location’s `name`. Use cycle detection and a max depth guard; fall back to site name when no location is set or on API failure.

2. **Location filter:** Collect **all descendant** location IDs using BFS over `GET /api/dcim/locations/?parent_id=…` with pagination (`next`).

3. **Documentation:** Authoritative technical detail lives in the module repo: [`project-zabake/zabbix-netbox/docs/design/LOCATION_HIERARCHY_RESOLUTION.md`](../../project-zabake/zabbix-netbox/docs/design/LOCATION_HIERARCHY_RESOLUTION.md). Reference implementation and tests: [`project-zabake/zabbix-netbox/scripts/netbox_location_hierarchy.py`](../../project-zabake/zabbix-netbox/scripts/netbox_location_hierarchy.py).

## Consequences

- **Positive:** Consistent Zabbix grouping for arbitrarily nested NetBox locations; filters include full subtrees.
- **Negative:** More NetBox API calls per device when resolving root (bounded by depth); BFS may call the API more than the old single-child fetch.
- **Follow-up:** Keep Ansible-embedded Python in `process_device.yml` / `fetch_all_devices.yml` aligned with `netbox_location_hierarchy.py` (or consolidate later).

## Links

- Wiki: [[03-Module-Project-Zabake-HMDL]]
- Collector context: [[datalake-collectors/NetBox-Loki]]
