# Collector: VMware

## Purpose

vCenter üzerinden datacenter / cluster / host / VM envanteri ve metrikleri; kapasite ve performans panelleri için temel veri. Özet: [collectors-purpose-cards](../../../datalake/docs/collectors-purpose-cards.md).

## Code locations

- Collectors: [datalake/collectors/VMware/](../../../datalake/collectors/VMware/)
- Örnekler: `vmware_*_collector.py`, `vmware_*_performance_metrics.py`, `discovery/vmware-discovery.py`, `deprecated/` altında eski discovery.
- JSON şemaları (kısmi): [datalake/SQL/json_schemas/VMware/](../../../datalake/SQL/json_schemas/VMware/)
- DDL / görünümler: [datalake/SQL/VMware/](../../../datalake/SQL/VMware/) (`01_tables`, `02_views`, `03_materialized_views`)

## Standards compliance

- **Güçlü:** Python collector’lar `data_type` ve `collection_timestamp` ile stdout JSON üretir; şablonla uyumlu çekirdek davranış.
- **Checklist:** VMware discovery için **Partial** — [collector_discovery_checklist](../../../datalake/docs/development-templates/collector_discovery_checklist.md) §2.3; hedef tablo adları `discovery_vmware_inventory_*` ile hizalanmalı.
- **NiFi:** `ExecuteStreamCommand` → `SplitJson` → `RouteOnAttribute` kalıbı dokümante edilmiş; üretim akış adları `etl-pipelines/` XML içinde aranmalıdır.

## Database

- Ham katman: `raw_vmware_*` tabloları (`SQL/VMware/01_tables/`).
- GUI sorguları çoğunlukla **legacy** aggregate tabloları kullanır: `datacenter_metrics`, `cluster_metrics`, `vmhost_metrics` (bkz. [vmware.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/vmware.py) başlık yorumları).

## Downstream (GUI / mock)

- [registry.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/registry.py) — `vmware_*`, `customer_vmware_*`, `energy_vcenter` kaynakları.
- Şema veya tablo değişikliklerinde [[../02-Module-Platform-GUI|Platform GUI]] ve [[../04-Module-WebUI-Mock|WebUI Mock]] senkron kuralına uy.

## Cross-module (project-zabake)

Doğrudan kod paylaşımı yok; vCenter envanteri GUI ve Grafana ile aynı DB’yi paylaşır. Zabbix/NetBox otomasyonu ([[../03-Module-Project-Zabake-HMDL|project-zabake]]) ayrı yaşam döngüsü.

## Gaps / debt

- `deprecated/discovery` altında eski script’ler.
- Legacy `datacenter_metrics` isimleri `raw_` standardı ile yan yana; uzun vadede görünüm veya migrate stratejisi checklist §4 ile uyumlu olmalı.

[[00-Index|← Index]]
