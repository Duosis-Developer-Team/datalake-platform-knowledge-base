# Schema-only: Zerto ve Vertiv PDU (collector gap)

## Durum

`datalake/SQL/All Tables/` ve `Datalake-Platform-GUI` sorgularında **`raw_zerto_*`** ve **`raw_vertiv_pdu_*`** tabloları tanımlı ve kullanılıyor; bu monorepo içinde **`datalake/collectors/` altında bu tabloları dolduran bir Python collector dosyası bulunamadı** (repo taraması: `zerto` / `vertiv` içeren `*.py` yok).

Bu sayfa bir **operasyonel ve dokümantasyon riski** kaydıdır: şema ve UI hazır olabilir, veri hattı başka bir repo, paketlenmiş job veya manuel import ile besleniyor olabilir.

## GUI referansları (örnek)

- Zerto: [backup.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/backup.py) — `raw_zerto_site_metrics`; [customer.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/customer.py) — `raw_zerto_vpg_metrics`.
- [registry.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/registry.py) — `backup_zerto_sites_latest`.

Vertiv: DDL kopyaları ayrıca [Datalake-Platform-GUI/docs/All Tables/](../../../Datalake-Platform-GUI/docs/All%20Tables/) altında (`raw_vertiv_pdu_*.sql`); `datalake/SQL/All Tables/` ile çapraz kontrol edin. GUI Python sorgularında `vertiv` referansı bulunmayabilir — tablolar şema hazırlığı aşamasında olabilir.

## Önerilen aksiyonlar

1. Veri kaynağını (harici script, eski branch, NiFi-only ingest) sahiplenip bu wiki sayfasına veya `datalake/docs/runbook/` altına ekleyin.
2. Collector bu repoya taşınırsa [collector_discovery_template](../../../datalake/docs/development-templates/collector_discovery_template.md) ile uyumlu JSON + şema ekleyin.
3. Tablo kullanımı kalktıysa GUI ve DDL temizliği için CR açın.

## Standards compliance

- **Gap:** Ingest tarafı kanıtlanmadan **Partial / Full atanamaz**.

[[00-Index|← Index]]
