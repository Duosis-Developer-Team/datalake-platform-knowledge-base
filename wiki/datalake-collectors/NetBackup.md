# Collector: NetBackup

## Purpose

NetBackup API ile job durumları ve disk pool metrikleri; yedekleme başarım ve kapasite. [collectors-purpose-cards](../../../datalake/docs/collectors-purpose-cards.md).

## Code locations

- Aktif: [netbackup_data_collector.py](../../../datalake/collectors/Netbackup/netbackup_data_collector.py) — JSON stdout, NiFi uyumu dokümanda belirtilmiş.
- [Deprecated/](../../../datalake/collectors/Netbackup/Deprecated/) — eski SQL / insert yaklaşımları ([checklist §3.1](../../../datalake/docs/development-templates/collector_discovery_checklist.md)).

## Standards compliance

- **Partial:** Güncel script JSON odaklı; deprecated klasör **Non-compliant** legacy örnekleri saklar.

## Database

- `raw_netbackup_jobs_metrics`, `raw_netbackup_disk_pools_metrics` — [backup.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/backup.py), [customer.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/customer.py).

## Downstream (GUI / mock)

- [registry.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/registry.py) — `backup_netbackup_pools_latest`.

## Cross-module (project-zabake)

Yok.

## Gaps / debt

- Deprecated script’lerin arşivlenmesi veya tam kaldırılması PR ile yapılmalı; yeni geliştirme sadece aktif collector üzerinden.

[[00-Index|← Index]]
