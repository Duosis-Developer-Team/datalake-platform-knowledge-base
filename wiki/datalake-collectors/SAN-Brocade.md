# Collector: SAN Brocade (REST)

## Purpose

Brocade Fabric OS REST API ile fabric envanteri ve port istatistikleri; IBM SAN eşleştirme için matcher. Mimari özet [architecture-overview](../../../datalake/docs/architecture-overview.md) (Brocade SAN).

## Code locations

- [san-collector.py](../../../datalake/collectors/Storage/IBM-SAN/san-collector.py) — Brocade REST, JSON snapshot dosyası üretebilir.
- [ibm-matcher.py](../../../datalake/collectors/Storage/IBM-SAN/ibm-matcher.py) — NiFi stdin JSON ile Brocade / IBM envanter eşlemesi (pandas).

## Standards compliance

- **Partial:** REST collector modern API kullanımı; çıktının NiFi `SplitJson` hattına uygun tek tip JSON dizisi olduğu dosya içi tasarımla doğrulanmalı.
- Matcher ayrı süreç (stdin işleme).

## Database

- GUI: `raw_brocade_port_status`, `raw_brocade_port_statistics`, `raw_brocade_san_fcport_1` — [brocade.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/brocade.py).

## Downstream (GUI / mock)

- Datacenter API brocade sorguları; değişikliklerde mock senkronu.

## Cross-module (project-zabake)

Yok.

## Gaps / debt

- SNMP tabanlı eski anlatım ile REST collector birlikte anılabiliyor; üretimde hangi API sürümünün (FOS) desteklendiği README’de sabitlenmeli.

[[00-Index|← Index]]
