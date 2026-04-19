# Collector: NetBox / Loki (inventory)

## Purpose

Loki (NetBox) üzerinden lokasyon, rack, cihaz tipi, cihaz ve VM envanteri; panellerde filtreleme ve eşleme. [collectors-purpose-cards](../../../datalake/docs/collectors-purpose-cards.md).

## Code locations

- [datalake/collectors/NetBox/](../../../datalake/collectors/NetBox/)
  - `loki_get_location.py`, `loki_get_rack.py`, `loki_get_device.py`, `loki_get_device-types.py`, `loki-get-vm.py`
- [README.md](../../../datalake/collectors/NetBox/README.md)
- Şema: [loki-get-vm.json](../../../datalake/SQL/json_schemas/NetBox/loki-get-vm.json)
- DDL örneği: [loki_get_virtualmachines.sql](../../../datalake/SQL/NetBox/loki_get_virtualmachines.sql)

## Standards compliance

- **Partial:** `loki-get-vm` için checklist §2.1 — discovery + UPSERT deseni; fiziksel tablo adı tam `discovery_*` olmayabilir.
- Diğer `loki_*.py` script’leri aynı disiplinle gözden geçirilmeli.

## Database

- `discovery_netbox_inventory_device`, `discovery_loki_location`, `discovery_loki_rack`, vb. — [discovery_rack.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/discovery_rack.py), Zabbix sorgularında join.

## Downstream (GUI / mock)

- Fiziksel envanter ve müşteri panelleri; `dc_service` ve `customer` sorguları.

## Cross-module (project-zabake)

**Yüksek:** [zabbix-netbox](../../../project-zabake/zabbix-netbox/README.md) NetBox ile Zabbix senkronu; datalake ayrıca API’den okuyup DB’ye yazar — tutarlılık için alan eşlemeleri koordine edilmeli.

**NetBox `dcim/locations` parent zinciri:** HMDL tarafında Zabbix host group / `DC_ID` için kullanılan isim, NetBox’ta **kök lokasyon** adıdır (üst üste `parent` takibi). Cihaz alt seviye bir lokasyondaysa (ör. DC18 → DH3 → Rack) etiket yine köke (ör. DC18) çözülür. Ayrıntı: [LOCATION_HIERARCHY_RESOLUTION.md](../../../project-zabake/zabbix-netbox/docs/design/LOCATION_HIERARCHY_RESOLUTION.md), [ADR-0005](../../adrs/ADR-0005-netbox-location-hierarchy-resolution.md).

## Gaps / debt

- Checklist’teki gibi `discovery_` önekli kanonik tablo adlarına geçiş veya görünüm katmanı.

[[00-Index|← Index]]
