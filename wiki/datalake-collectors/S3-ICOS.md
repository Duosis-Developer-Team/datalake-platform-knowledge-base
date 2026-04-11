# Collector: S3 ICOS

## Purpose

Vault inventory/metrics ve pool metrics; nesne depolama kapasite ve kullanım izleme. [collectors-purpose-cards](../../../datalake/docs/collectors-purpose-cards.md).

## Code locations

- [s3-collector.py](../../../datalake/collectors/Storage/S3/s3-collector.py)
- [README.md](../../../datalake/collectors/Storage/S3/README.md)

## Standards compliance

- **Partial:** REST tabanlı collector; `configuration_file.json.example` içinde `s3icos` bloğu.
- Çıktı alanları ve `data_type` kullanımı üretim NiFi ile birlikte doğrulanmalı.

## Database

- `raw_s3icos_pool_metrics`, `raw_s3icos_vault_metrics`, `raw_s3icos_vault_inventory` — [s3.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/s3.py) (customer-api ve src kopyaları paralel).

## Downstream (GUI / mock)

- Müşteri ve datacenter S3 panelleri; registry’de doğrudan `raw_s3icos_*` kaynaklı sorgular.

## Cross-module (project-zabake)

Yok.

## Gaps / debt

- S3 proje standartları (workspace kuralı) ile veri içeriği ayrıştırılmalı; bucket politikası ve PII bu wiki kapsamı dışında operasyonel konudur.

[[00-Index|← Index]]
