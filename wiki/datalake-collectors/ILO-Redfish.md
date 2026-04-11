# Collector: HPE iLO (Redfish)

## Purpose

iLO Redfish API ile envanter ve metrik (CPU, bellek, disk, güç vb.); `data_type` ile çoklu hedef tabloya yönlendirme.

## Code locations

- [redfish_collector.py](../../../datalake/collectors/ILO/Redfis-API/redfish_collector.py)
- [README.md](../../../datalake/collectors/ILO/Redfis-API/README.md)

## Standards compliance

- **Partial:** [collector_discovery_checklist §2.2](../../../datalake/docs/development-templates/collector_discovery_checklist.md) — Avro şema NiFi’de; `data_type` routing; tablo adları tam `raw_ilo_*` / `discovery_ilo_*` olmayabilir (§3.2).

## Database

- Mantıksal: `raw_ilo_metrics_*`, `discovery_ilo_inventory_*` (README ve checklist).
- Fiziksel tablo adları legacy olabilir — DDL `SQL/` ve `All Tables` ile çapraz kontrol.

## Downstream (GUI / mock)

- GUI’de doğrudan `raw_ilo_*` araması sınırlı olabilir; Grafana veya özel paneller öncelikli.

## Cross-module (project-zabake)

Genelde yok; cihaz envanteri NetBox ile birlikte düşünülür.

## Gaps / debt

- Önek standardizasyonu ve UPSERT anahtarlarının dokümante DDL ile eşleşmesi.

[[00-Index|← Index]]
