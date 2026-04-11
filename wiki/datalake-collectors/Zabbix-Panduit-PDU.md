# Collector: Zabbix — Panduit PDU

## Purpose

Zabbix’ten Panduit PDU öğeleri; güç, faz, ortam sensörleri vb. çoklu `data_type` ile tek akışta toplanır.

## Code locations

- [zabbix-get-pdu-data.py](../../../datalake/collectors/Zabbix/Panduit-PDU/zabbix-get-pdu-data.py)
- [README.md](../../../datalake/collectors/Zabbix/Panduit-PDU/README.md)

## Standards compliance

- **Partial:** `collection_timestamp` ve çok sayıda `data_type` (`pdu_inventory`, `pdu_*_metrics`) — şablon §2 ile uyumlu yönlendirme için uygun.
- Script adında tire (`zabbix-get-pdu-data.py`) şablon §1.2’deki `*-metrics.py` ile tam örtüşmüyor; kabul edilebilir sapma veya yeniden adlandırma kararı.

## Database

- `raw_panduit_pdu_*` — [datalake/SQL/All Tables/](../../../datalake/SQL/All%20Tables/) altında tanımlar (ör. `raw_panduit_pdu_metrics_global.sql`).

## Downstream (GUI / mock)

- Özel dashboard yoksa Grafana veya genel raporlama; GUI registry’de doğrudan referans aranmalı (şu an çoğunlukla ham tablo).

## Cross-module (project-zabake)

Zabbix host/grup tanımları otomasyonla yönetilebilir.

## Gaps / debt

- GUI query eşlemesi net değilse operasyonel kullanım Grafana tarafında kalıyor olabilir — doğrulanmalı.

[[00-Index|← Index]]
