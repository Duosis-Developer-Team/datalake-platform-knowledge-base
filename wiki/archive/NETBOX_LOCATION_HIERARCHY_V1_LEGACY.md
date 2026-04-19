# Archive: NetBox location mapping (v1 single-step parent)

> **Superseded by:** [ADR-0005](../adrs/ADR-0005-netbox-location-hierarchy-resolution.md) and [zabbix-netbox/docs/design/LOCATION_HIERARCHY_RESOLUTION.md](../../../project-zabake/zabbix-netbox/docs/design/LOCATION_HIERARCHY_RESOLUTION.md)

**v1 behavior (obsolete):** `get_location_name` returned only the **immediate parent** name when present, or the current location / site otherwise. This broke multi-level trees: a device under `Rack` → `DH3` → `DC18` could be labeled as `DH3` for Zabbix host groups instead of `DC18`.

**v1 filter behavior (obsolete):** Location filter by name included only **one level** of child location IDs.

This file is retained as a short historical pointer; do not use it for implementation.
