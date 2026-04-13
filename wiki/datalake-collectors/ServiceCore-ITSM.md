# ServiceCore ITSM

**Source:** ServiceCore (Bulutistan Operations Support API) — incidents, service requests, and users.

**Type:** Discovery (mutable tickets and user catalog) — UPSERT into `discovery_*` tables; incremental OData filters reduce load on incidents and service requests. Users are fetched in full via `User/GetAllUsers` (OData `$filter=1 eq 1`) unless disabled.

## Run (explicit CLI parameters)

Same style as other collectors (e.g. Zabbix Network, S3): **no** `--config` / JSON path. Pass `--api-url`, `--api-key`, and optional tuning flags:

```text
python3 /path/to/collectors/ServiceCore/servicecore-discovery.py \
  --api-url https://operationsupportapi.bulutistan.com/api/v1 \
  --api-key "<API_KEY>" \
  --lookback-hours 24 \
  --page-size 100
```

Optional: `--skip-users` — skip `User/GetAllUsers` (smaller runs / smoke tests; stdout will only contain incidents and service requests).

NiFi `ExecuteStreamCommand` should supply these arguments (often via Parameter Context for secrets). See [README.md](../../../datalake/collectors/ServiceCore/README.md) for the full option list.

[configuration_file.json.example](../../../datalake/collectors/configuration_file.json.example) documents equivalent keys as a **template** for operators; the script does not read that file.

**Repo paths**

| Artifact | Path |
|----------|------|
| Collector script | [servicecore-discovery.py](../../../datalake/collectors/ServiceCore/servicecore-discovery.py) |
| README | [README.md](../../../datalake/collectors/ServiceCore/README.md) |
| Bronze DDL | [raw_servicecore_logs.sql](../../../datalake/SQL/ServiceCore/raw_servicecore_logs.sql) |
| Silver DDL (incidents) | [discovery_servicecore_incidents.sql](../../../datalake/SQL/ServiceCore/discovery_servicecore_incidents.sql) |
| Silver DDL (SR) | [discovery_servicecore_servicerequests.sql](../../../datalake/SQL/ServiceCore/discovery_servicecore_servicerequests.sql) |
| Silver DDL (users) | [discovery_servicecore_users.sql](../../../datalake/SQL/ServiceCore/discovery_servicecore_users.sql) |
| Avro (single contract) | [servicecore-discovery.json](../../../datalake/SQL/json_schemas/ServiceCore/servicecore-discovery.json) — record `ServiceCoreDiscovery` |

**Tables**

| Layer | Table | Notes |
|-------|--------|--------|
| Bronze | `raw_servicecore_logs` | `endpoint_type`, `raw_content` JSONB, `collected_at` |
| Silver | `discovery_servicecore_incidents` | PK `ticket_id`; latest state |
| Silver | `discovery_servicecore_servicerequests` | PK `service_request_id`; latest state |
| Silver | `discovery_servicecore_users` | PK `user_id`; join to `OrgUserId` / `RequesterId` on tickets |

## `data_type` values

| Value | Meaning | UPSERT key | Silver table |
|-------|---------|------------|--------------|
| `servicecore_inventory_incident` | ITSM incident ticket | `ticket_id` | `discovery_servicecore_incidents` |
| `servicecore_inventory_servicerequest` | ITSM service request | `service_request_id` | `discovery_servicecore_servicerequests` |
| `servicecore_inventory_user` | User directory row | `user_id` | `discovery_servicecore_users` |

## Stdout JSON (sparse)

- **Sparse objects:** Each array element includes only keys that belong to that `data_type`, plus `collection_time`. Keys with null values are omitted from JSON (further shrinking output). There is **no** cross-type null padding (no incident row carrying `user_id: null` for every user field).
- **Avro file (`ServiceCoreDiscovery`):** Still a **single** schema listing **all** possible fields (nullable unions). NiFi **JsonTreeReader** uses it to map JSON property names to column names and types; **missing** JSON keys are treated as **null** when writing to PostgreSQL.
- **Tenant linkage:** Incidents and SR include `org_user_support_account_name` / `org_user_support_account_id` when the API provides them. User rows provide `user_id`, `email`, `full_name`, `is_enabled`, `job_title`, `soft_deleted` for joins.

### Field sets (collector output keys, snake_case)

Incident rows typically include: `data_type`, `ticket_id`, `subject`, `state`, `state_text`, `status_id`, `status_name`, `priority_id`, `priority_name`, `category_id`, `category_name`, `org_user_id`, `org_users_name`, `agent_id`, `agent_group_id`, `agent_group_name`, `agent_full_name`, `org_user_support_account_name`, `org_user_support_account_id`, `sla_policy_name`, `company_name`, `times_reopen`, `is_active`, `is_deleted`, `is_merged`, `created_date`, `last_updated_date`, `target_resolution_date`, `closed_and_done_date`, `code_prefix`, `guid`, `description_text_format`, `custom_fields_json`, `attachment_files`, `origin_from_name`, `collection_time` (only keys with non-null values after normalization are emitted, except booleans and zero).

Service request rows: `data_type`, `service_request_id`, `service_request_name`, `subject`, `requester_id`, `requester_full_name`, `org_users_name`, state/status/priority/category fields, agent fields, tenant fields, `tags`, dates, `is_active`, `is_deleted`, `code_prefix`, `guid`, `request_description_text_format`, `custom_fields_json`, `attachment_files`, `collection_time`.

User rows: `data_type`, `user_id`, `email`, `full_name`, `job_title`, `is_enabled`, `soft_deleted`, `collection_time`.

## API endpoints (collector)

| Endpoint | OData filter (default) |
|----------|-------------------------|
| `Incident/GetAll` | `LastUpdatedDate ge <UTC>` |
| `ServiceRequest/GetAll` | `RequestDate ge <UTC>` |
| `User/GetAllUsers` | `1 eq 1` (full catalog; omit with `--skip-users`) |

**Why service requests may be missing in a run:** Incidents are selected by **last update** time; service requests are selected by **request creation** time (`RequestDate`). A short `lookback-hours` window can return incidents that were updated recently but **no** SR whose `RequestDate` falls in that window. That is expected. For historical SR coverage, increase lookback or run a dedicated backfill.

**Initial load / backfill:** `lookback-hours` limits incidents and SR to a rolling window; it does **not** guarantee a full historical snapshot. `User/GetAllUsers` returns the full user catalog each run (unless `--skip-users`). Plan separate jobs or larger lookback for one-time backfills.

## NiFi

- **JsonTreeReader:** one **Schema Text** from `servicecore-discovery.json` for all routes when compatible with your NiFi/DBCP settings (same reader, **RouteOnAttribute** by `data_type`, three **PutDatabaseRecord** UPSERTs).
- **TIMESTAMPTZ columns vs Avro:** Silver DDL uses `TIMESTAMPTZ` for all API datetime fields that land in `discovery_servicecore_incidents` / `discovery_servicecore_servicerequests` / `discovery_servicecore_users` (see DDL files). The shared `servicecore-discovery.json` maps those JSON string instants to Avro **timestamp-millis** so **PutDatabaseRecord** binds JDBC timestamps, not `varchar`. Fields aligned with DDL: `created_date`, `last_updated_date`, `target_resolution_date`, `closed_and_done_date` (incidents); `request_date`, `target_resolution_date`, `target_response_date`, `deleted_date` (service requests); `collection_time` (all three).
- **JsonTreeReader formats:** API strings vary: `collection_time` often includes offset (`...+00:00`); other dates may omit timezone and use variable fractional precision (e.g. `2026-04-12T12:32:42.04`). Configure **Timestamp** (and **Date** if used) patterns so every variant used in your feed parses; if one pattern is insufficient, add **UpdateRecord** / **ConvertRecord** or normalize in the collector. Without successful parse, values stay strings and **PutDatabaseRecord** fails on `TIMESTAMPTZ` columns.
- **PutDatabaseRecord** UPSERT keys: `ticket_id` (incidents) / `service_request_id` (service requests) / `user_id` (users).
- If the processor rejects columns not present on the target table, narrow fields per route (`UpdateRecord` / `JoltTransformJSON`) or use options that ignore unmatched fields — see README.

## Compliance

- Follows [[00-Index]] naming: `raw_*` + `discovery_*`
- Config template: [configuration_file.json.example](../../../datalake/collectors/configuration_file.json.example) `ServiceCore` block (documentation only)
- Operational standard: **explicit CLI flags** (`--api-url`, `--api-key`, …) + **single Avro contract** (`ServiceCoreDiscovery`) for NiFi column typing; **sparse** stdout JSON per `data_type`

## See also

- [[00-Index|Collectors index]]
- [[../01-Module-Datalake-Core|Module: datalake (core)]]
