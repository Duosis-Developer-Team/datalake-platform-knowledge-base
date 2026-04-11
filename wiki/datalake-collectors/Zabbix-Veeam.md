# Collector: Zabbix — Veeam

## Purpose

Zabbix üzerinden Veeam ile ilgili öğelerin toplanması; yedekleme altyapısı metrikleri (proxy, repo, job oturumları vb.). Script: [get_veeam_data.py](../../../datalake/collectors/Zabbix/Veeam/get_veeam_data.py) (`data_type`, `collection_time`).

## Code locations

- [datalake/collectors/Zabbix/Veeam/](../../../datalake/collectors/Zabbix/Veeam/)
- Yardımcı: [veeampy.py](../../../datalake/collectors/Zabbix/Veeam/veeampy.py)
- `old_scripts/` — PHP örnekleri; referans.

## Standards compliance

- **Partial:** JSON çıktı ve `data_type` mevcut; şema dosyalarının `SQL/json_schemas` altında tamamlığı kontrol edilmeli.

## Database

- `raw_veeam_*` tabloları (`SQL/All Tables`: örn. `raw_veeam_sessions`, `raw_veeam_repositories_states`, …).
- GUI: [backup.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/backup.py), [customer.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/customer.py); [registry.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/registry.py) `backup_veeam_repos_latest`.

## Downstream (GUI / mock)

- Backup dashboard ve müşteri özetleri; API sözleşmesi değişince mock güncelle.

## Cross-module (project-zabake)

Dolaylı: Zabbix item/host tanımları zabake ile yönetilebilir; datalake yalnızca metrik okur.

## Gaps / debt

- JSON örnek dosyaları (`output.json`, vb.) repoda; gizli bilgi içermediğinden emin olunmalı veya `.gitignore` politikası netleştirilmeli.

[[00-Index|← Index]]
