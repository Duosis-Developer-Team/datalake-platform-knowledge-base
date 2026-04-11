# Collector: IBM Storage (Storwize / Spectrum)

## Purpose

Cihaz kullanımı ve iostats çıktılarının işlenmesi; havuz / volume kapasiteleri ve trend. [collectors-purpose-cards](../../../datalake/docs/collectors-purpose-cards.md).

## Code locations

- [datalake/collectors/IBM/IBM_Storage/](../../../datalake/collectors/IBM/IBM_Storage/)
- `IBM_storage_pysvc.py`, `IBM_storage_cli.py`, `IBM_storage_class.py`, `IBM_storage_parse.py`
- SSH / iostats: `SSH/iostats/` (`reader.py`, `IBM_io_vdisk.py`, vb.)
- `DEPRICATED_*` — eski indirme yolları; referans için duruyor.

## Standards compliance

- **Legacy / mixed:** Çoğunlukla API/CLI/SSH ile veri çekme ve parse; stdout JSON tek tip değil. Checklist §3.1 (SQL batch / özel format) uyarısı geçerli olabilir.

## Database

- GUI: `raw_ibm_storage_system`, `raw_ibm_storage_system_stats`, `raw_ibm_storage_vdisk` — [ibm_storage.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/ibm_storage.py), ayrıca `db_service` içi sorgular.
- DDL örnekleri: `datalake/SQL/All Tables/` altında IBM ile ilgili dosyalar.

## Downstream (GUI / mock)

- Datacenter ve müşteri panelleri storage kapasite birleşimleri; şema değişince GUI ve mock güncellenmeli.

## Cross-module (project-zabake)

Doğrudan bağ yok; envanter eşlemesi NetBox üzerinden dolaylı.

## Gaps / debt

- pysvc vs CLI vs SSH — üç yolun operasyonel önceliği ve test kapsamı dokümante edilmeli.
- `raw_*` tabloları ile collector çıktı formatının birebir eşlemesi şema dosyalarıyla doğrulanmalı (`SQL/json_schemas` genişletmesi önerilir).

[[00-Index|← Index]]
