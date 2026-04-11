# Datalake collectors — index

Cross-module catalog: each row links to a dedicated wiki page. Authoritative standards live in [collector template](../../../datalake/docs/development-templates/collector_discovery_template.md) and [compliance checklist](../../../datalake/docs/development-templates/collector_discovery_checklist.md) under `datalake/`.

**Naming note:** The platform aims for `raw_*` (time series) and `discovery_*` (inventory / UPSERT) table prefixes. Many production tables still use legacy names (`datacenter_metrics`, `nutanix_cluster_metrics`, `zabbix_*_metrics`, etc.). New work should follow the template; renames are incremental.

| Collector family | Repo path | Compliance (summary) | Primary DB / GUI touchpoints | Detail page |
|------------------|-----------|----------------------|------------------------------|-------------|
| VMware | `datalake/collectors/VMware/` | **Partial** — modern JSON + `data_type`; DDL under `SQL/VMware/`; discovery per checklist | `datacenter_metrics`, `cluster_metrics`, `vmhost_metrics`, `raw_vmware_*` → `datacenter-api` [vmware.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/vmware.py), `registry.py` | [[VMware]] |
| Nutanix | `datalake/collectors/Nutanix/` | **Partial** — Prism API scripts; script names mix `*_dyn.py` and legacy `*_old` | `nutanix_cluster_metrics` (and related) → [nutanix.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/nutanix.py) | [[Nutanix]] |
| IBM HMC | `datalake/collectors/IBM/` (non-Storage) | **Legacy / mixed** — performance/energy pipelines; some JSON/config-driven | `ibm_server_general`, `ibm_vios_general`, `ibm_lpar_general`, `ibm_server_power` → [ibm.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/ibm.py), [energy.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/energy.py) | [[IBM-HMC]] |
| IBM Storage | `datalake/collectors/IBM/IBM_Storage/` | **Legacy / mixed** — pysvc, SSH iostats, parsers | `raw_ibm_storage_system`, `raw_ibm_storage_system_stats`, `raw_ibm_storage_vdisk` → [ibm_storage.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/ibm_storage.py), `db_service` | [[IBM-Storage]] |
| SAN Brocade | `datalake/collectors/Storage/IBM-SAN/` | **Partial** — REST collector JSON snapshot; matcher for fabric mapping | `raw_brocade_*` → [brocade.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/brocade.py) | [[SAN-Brocade]] |
| S3 ICOS | `datalake/collectors/Storage/S3/` | **Partial** — REST collector; aligns with S3 GUI module | `raw_s3icos_*` → [s3.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/s3.py) | [[S3-ICOS]] |
| Zabbix — Network | `datalake/collectors/Zabbix/Network/` | **Partial** — JSON + `data_type` + timestamp | `zabbix_network_device_metrics`, `zabbix_network_interface_metrics` + `discovery_netbox_inventory_device` joins → [zabbix_network.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/zabbix_network.py) | [[Zabbix-Network]] |
| Zabbix — Veeam | `datalake/collectors/Zabbix/Veeam/` | **Partial** — `get_veeam_data.py` JSON | `raw_veeam_*` → [backup.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/backup.py), [customer.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/customer.py) | [[Zabbix-Veeam]] |
| Zabbix — Panduit PDU | `datalake/collectors/Zabbix/Panduit-PDU/` | **Partial** — JSON + multiple `data_type` routes | `raw_panduit_pdu_*` (see `SQL/All Tables`) | [[Zabbix-Panduit-PDU]] |
| NetBackup | `datalake/collectors/Netbackup/` | **Partial** — modern script outputs NiFi-oriented JSON (see module docstring) | `raw_netbackup_jobs_metrics`, `raw_netbackup_disk_pools_metrics` → backup/customer queries | [[NetBackup]] |
| NetBox / Loki | `datalake/collectors/NetBox/` | **Partial** — discovery template + schema for VM; checklist entry | `discovery_netbox_*`, `discovery_loki_*` → discovery_rack, customer, zabbix joins | [[NetBox-Loki]] |
| iLO Redfish | `datalake/collectors/ILO/Redfis-API/` | **Partial** — checklist: Avro / `data_type` routing in NiFi | `ilo_*` / logical `raw_ilo_*` per README | [[ILO-Redfish]] |
| Schema-only (Zerto / Vertiv) | *no `collectors/` script in this repo* | **Gap** — DDL + GUI references exist | `raw_zerto_*`, `raw_vertiv_pdu_*` | [[Schema-Only-Zerto-Vertiv]] |

**Not:** GUI’de [zabbix_storage.py](../../../Datalake-Platform-GUI/services/datacenter-api/app/db/queries/zabbix_storage.py) `zabbix_storage_*` tablolarını kullanır; bu monorepo içinde `datalake/collectors/` altında bu tabloları üreten bir script **bulunamadı** — ingest kaynağı ayrıca doğrulanmalıdır ([[Schema-Only-Zerto-Vertiv]] ile aynı “gap” sınıfı).

## Cross-repo

- **GUI:** `Datalake-Platform-GUI` — `services/datacenter-api/app/db/queries/` and `src/queries/` (parallel copies in some services).
- **Mock:** `datalake-platform-webui-mock` — keep API contracts aligned per [[../00-Platform-Overview|Platform Overview]].
- **Automation:** `project-zabake` — Zabbix ↔ NetBox (Loki); feeds the same sources datalake collectors read (indirect).

## See also

- [[../01-Module-Datalake-Core|Module: datalake (core)]]
- [Collectors purpose cards](../../../datalake/docs/collectors-purpose-cards.md)
