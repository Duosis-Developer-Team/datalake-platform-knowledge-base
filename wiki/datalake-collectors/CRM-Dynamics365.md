# CRM — Dynamics 365 Discovery Collector

**Compliance level:** Full — ServiceCore-style unified script + flow

## Overview

The Dynamics 365 CRM collector uses a **single discovery script** (`crm-dynamics-discovery.py`) that emits **six** `data_type` variants into a single JSON array on stdout. A **single NiFi flow** routes each record to its dedicated PostgreSQL table via `RouteOnAttribute` + `PutDatabaseRecord` (UPSERT).

Scope is **realized sales only** (no invoices, contracts, opportunities, or quotes in the collector or DB). See **ADR-0010**.

This is the same pattern as the ServiceCore ITSM collector.

## Architecture

```
Dynamics 365 OData v4 → crm-dynamics-discovery.py (OAuth2 CC) → stdout JSON
  → NiFi ExecuteStreamCommand → SplitJson → EvaluateJsonPath (data_type)
  → RouteOnAttribute (6 routes) → PutDatabaseRecord (UPSERT) → PostgreSQL
  → discovery_crm_* tables → customer-api / datacenter-api → GUI
```

## Script location

```
datalake/collectors/CRM/Dynamics365/crm-dynamics-discovery.py
```

## Data Types and UPSERT Keys

| `data_type` | UPSERT key | Category | PostgreSQL table |
|---|---|---|---|
| `crm_inventory_account` | `accountid` | Master | `discovery_crm_accounts` |
| `crm_inventory_product` | `productid` | Catalog | `discovery_crm_products` |
| `crm_inventory_pricelevel` | `pricelevelid` | Catalog | `discovery_crm_pricelevels` |
| `crm_inventory_productpricelevel` | `productpricelevelid` | Catalog | `discovery_crm_productpricelevels` |
| `crm_inventory_salesorder` | `salesorderid` | Realized sales | `discovery_crm_salesorders` |
| `crm_inventory_salesorderdetail` | `salesorderdetailid` | Realized sales | `discovery_crm_salesorderdetails` |

## Incremental Strategy

| Category | Filter |
|---|---|
| Master (Accounts) | `statecode eq 0` (active) — full snapshot |
| Catalog (Products) | `statecode eq 0` (active) — full snapshot |
| Catalog (PriceLevels, ProductPriceLevels) | No filter — full snapshot |
| Realized sales (SalesOrders) | `(modifiedon ge <UTC lookback>) and (statecode eq 3 or statecode eq 4)` when incremental; state filter always |

Use `--full-snapshot` for initial sales backfill (all Fulfilled/Invoiced orders regardless of `modifiedon`).

## NiFi Flow

Single Processor Group `CRM Dynamics Discovery`:

1. `ExecuteStreamCommand` — runs the script every 30 minutes
2. `SplitJson ($.*)`
3. `EvaluateJsonPath` — extracts `data_type` to FlowFile attribute
4. `RouteOnAttribute` — **6** routes by `data_type`
5. 6× `PutDatabaseRecord` — Statement Type = UPSERT; same `JsonTreeReader` with `crm-dynamics-discovery.json` schema

NiFi XML grep note: `datalake/SQL/CRM/NiFi-scope-note.md`.

## Avro Schema

`datalake/SQL/json_schemas/CRM/crm-dynamics-discovery.json`  
Record name: `CrmDynamicsDiscovery` (namespace `datalake.crm`)  
Unified sparse record: all entity field sets as nullable unions in one record.

## Customer Identity Resolution

CRM account IDs are mapped to canonical platform customer keys via `discovery_crm_customer_alias`.

- Seeded by `SQL/CRM/seed_customer_alias_from_accounts.sql` (auto, best-effort name match)
- Corrected via GUI Settings → Customer Alias (promotes to `source = 'manual'`)
- Decision documented in ADR-0008

## Product category alias (GUI)

Hybrid mapping from CRM products to service categories: YAML rules + `discovery_crm_product_category_alias`
(see DDL under `datalake/SQL/CRM/`). Operators can override rows with `source = 'manual'`.

## GUI Consumption

| Feature | Source |
|---|---|
| Customer View → Billing (realized sales KPIs) | `customer-api /customers/{name}/sales/*` |
| DC sales potential | `datacenter-api /datacenters/{dc}/sales-potential` (v2 where deployed) |
| Settings → Customer Alias | `customer-api /crm/aliases` |
| Settings → Product category alias | `admin-api /api/v1/crm/product-categories` |

## Unit Tests

```bash
cd datalake/collectors/CRM/Dynamics365
python3 -m pytest test_crm_dynamics_discovery.py -v
```

## References

- Script README: `datalake/collectors/CRM/Dynamics365/README.md`
- ADR-0008: `adrs/ADR-0008-crm-customer-identity-resolution.md`
- ADR-0010: `adrs/ADR-0010-crm-realized-sales-only-scope.md`
- Reference: ServiceCore ITSM collector (`datalake/collectors/ServiceCore/`)
