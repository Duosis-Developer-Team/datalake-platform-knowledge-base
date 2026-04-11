# Collector: Nutanix

## Purpose

Prism API ile cluster / host / VM ve data protection (snapshot) metrikleri; kaynak kullanımı ve koruma görünürlüğü. [collectors-purpose-cards](../../../datalake/docs/collectors-purpose-cards.md).

## Code locations

- [datalake/collectors/Nutanix/](../../../datalake/collectors/Nutanix/)
- Aktif tarz: `nutanix_cluster_dyn.py`, `nutanix_host_dyn.py`, `nutanix_vm_dyn.py`
- Eski / yedek: `Nutanix_*_old.py`, `Nutanix_Snapshot_*.py`, `NutanixTest.py`

## Standards compliance

- **Partial:** Prism API üzerinden metrik toplama hedefi şablona uygun; dosya adlarında `-metrics` / `-stats` son eki tutarlı değil (`*_dyn.py`, `*_old`).
- JSON stdout + `data_type` her scriptte aynı disiplinle doğrulanmalı (script bazında inceleme).

## Database

- Merkezi GUI kaynağı: `nutanix_cluster_metrics` ([nutanix.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/nutanix.py)).
- DDL: [datalake/SQL/create_nutanix_tables.sql](../../../datalake/SQL/create_nutanix_tables.sql) ve `SQL/All Tables` altındaki ilgili tanımlar.

## Downstream (GUI / mock)

- [registry.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/registry.py) — `nutanix_*`, `customer_nutanix_*`.
- `raw_nutanix_*` önekine geçiş şablon hedefi; mevcut tablolar legacy isimler taşıyabilir.

## Cross-module (project-zabake)

Doğrudan entegrasyon yok; NetBox cihaz eşlemesi diğer modüller üzerinden.

## Gaps / debt

- Klasörde birden fazla snapshot / metrik script’i ve `.save` yedekleri — hangi üretim yolunun aktif olduğu operasyonel olarak netleştirilmeli.
- İsimlendirme şablon [collector_discovery_template](../../../datalake/docs/development-templates/collector_discovery_template.md) §1.2 ile hizalanmalı.

[[00-Index|← Index]]
