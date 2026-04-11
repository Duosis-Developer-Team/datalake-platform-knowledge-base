# Collector: IBM HMC (Power)

## Purpose

HMC üzerinden server / VIOS / LPAR envanteri ve kullanım; enerji metrikleri. Kurumsal Power altyapısı görünürlüğü. [collectors-purpose-cards](../../../datalake/docs/collectors-purpose-cards.md).

## Code locations

- [datalake/collectors/IBM/](../../../datalake/collectors/IBM/) — Storage alt klasörü hariç HMC odaklı script’ler:
  - `IBM_hmc_performance_data_collector.py`, `IBM_hmc_performance_data_extractor_and_report_generator.py`
  - `IBM_hmc_energy_temperature_data_extractor.py`, `IBM_hmc_Stats_Processor.py`
  - `IBM_generate_filtered_insert_commands.py` (SQL üretimi — legacy riski)
- HMC + S3 köprüsü: [hmc-s3-collector.py](../../../datalake/collectors/IBM/S3/hmc-s3-collector.py) (ICOS ile karıştırılmamalı; HMC verisinin nesne depoya aktarımı için ayrı kullanım senaryosu).
- `IBM/S3/tmp/` — geçici / deneme script’leri; üretim sınırı dokümante edilmeli.

## Standards compliance

- **Legacy / mixed:** Performans ve enerji boru hatlarında JSON/config kullanımı var; `IBM_generate_filtered_insert_commands.py` tarzı doğrudan SQL çıktısı [checklist §3.1](../../../datalake/docs/development-templates/collector_discovery_checklist.md) **Non-compliant** örüntüsüne girer.

## Database

- GUI: `ibm_server_general`, `ibm_vios_general`, `ibm_lpar_general`, `ibm_server_power` — [ibm.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/ibm.py), [energy.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/energy.py).
- DDL: [datalake/SQL/create_hmc_tables.sql](../../../datalake/SQL/create_hmc_tables.sql).

## Downstream (GUI / mock)

- [registry.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/registry.py) — `ibm_*`, `energy_ibm`, `customer_ibm_*`.

## Cross-module (project-zabake)

Power / envanter otomasyonu GUI ile aynı DB okumasını paylaşabilir; zabake playbook’ları kaynak sistemde değişiklik yapar, datalake ingest ayrı kalır.

## Gaps / debt

- SQL batch üreten yardımcı script’lerin NiFi `PutDatabaseRecord` + JSON modeline taşınması önerilir.
- `tmp/` ve çoklu extractor — hangi zincirin kanonik olduğu runbook’ta net olmalı.

[[00-Index|← Index]]
