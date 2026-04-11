# Collector: Zabbix — Network (Fortinet / Sophos)

## Purpose

Zabbix API üzerinden ağ cihazı metrikleri; güvenlik katmanı kapasite ve durum. Kart: [collectors-purpose-cards](../../../datalake/docs/collectors-purpose-cards.md) (“Network (Fortinet, Sophos)”).

## Code locations

- [zabbix-network.py](../../../datalake/collectors/Zabbix/Network/zabbix-network.py)
- [README.md](../../../datalake/collectors/Zabbix/Network/README.md)
- Legacy PHP: [Zabbix_Hosts_Networks.php](../../../datalake/collectors/Zabbix/Zabbix_Hosts_Networks.php), [ZabbixApi.php](../../../datalake/collectors/Zabbix/ZabbixApi.php) — modern yol Python öncelikli sayılmalı.

## Standards compliance

- **Partial:** `data_type` (`network_device`, `network_interface`), `collection_timestamp` ile JSON; şablonla uyumlu.
- PHP script’ler legacy; yeni geliştirme Python hattında.

## Database

- `zabbix_network_device_metrics`, `zabbix_network_interface_metrics` — NetBox `loki_id` ile birleşim: [zabbix_network.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/zabbix_network.py).
- Join hedefi: `discovery_netbox_inventory_device`.

## Downstream (GUI / mock)

- Datacenter API Zabbix network sorguları; fiziksel envanter ile DC filtreleme.

## Cross-module (project-zabake)

**Yüksek:** Zabbix host şablonları ve NetBox envanteri [project-zabake](../../../project-zabake/README.md) ile yönetilir; datalake aynı Zabbix API’den okur — kaynak sistem tek, ingest pipeline ayrı.

## Gaps / debt

- Tablo adları `raw_zabbix_*` yerine `zabbix_*` legacy biçimi (checklist §3.2).

[[00-Index|← Index]]
