# CRM — Dynamics 365 Discovery Collector

**Compliance level:** Full — ServiceCore-style unified script + flow

## Overview

The Dynamics 365 CRM collector uses a **single discovery script** (`crm-dynamics-discovery.py`) that emits 14 `data_type` variants into a single JSON array on stdout. A **single NiFi flow** routes each record to its dedicated PostgreSQL table via `RouteOnAttribute` + `PutDatabaseRecord` (UPSERT).

This is the same pattern as the ServiceCore ITSM collector.

## Architecture

```
Dynamics 365 OData v4 → crm-dynamics-discovery.py (OAuth2 CC) → stdout JSON
  → NiFi ExecuteStreamCommand → SplitJson → EvaluateJsonPath (data_type)
  → RouteOnAttribute (14 routes) → PutDatabaseRecord (UPSERT) → PostgreSQL
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
| `crm_inventory_opportunity` | `opportunityid` | Sales | `discovery_crm_opportunities` |
| `crm_inventory_opportunityproduct` | `opportunityproductid` | Sales | `discovery_crm_opportunityproducts` |
| `crm_inventory_quote` | `quoteid` | Sales | `discovery_crm_quotes` |
| `crm_inventory_quotedetail` | `quotedetailid` | Sales | `discovery_crm_quotedetails` |
| `crm_inventory_salesorder` | `salesorderid` | Sales | `discovery_crm_salesorders` |
| `crm_inventory_salesorderdetail` | `salesorderdetailid` | Sales | `discovery_crm_salesorderdetails` |
| `crm_inventory_invoice` | `invoiceid` | Sales | `discovery_crm_invoices` |
| `crm_inventory_invoicedetail` | `invoicedetailid` | Sales | `discovery_crm_invoicedetails` |
| `crm_inventory_contract` | `contractid` | Contracts | `discovery_crm_contracts` |
| `crm_inventory_contractdetail` | `contractdetailid` | Contracts | `discovery_crm_contractdetails` |

## Incremental Strategy

| Category | Filter |
|---|---|
| Master (Accounts) | No filter — always full snapshot |
| Catalog (Products, PriceLevels, PPL) | No filter — always full snapshot |
| Sales funnel | `$filter=modifiedon ge <UTC lookback>` (default 24h) |
| Contracts | `$filter=modifiedon ge <UTC lookback>` (default 24h) |

Use `--full-snapshot` for initial backfill.

## NiFi Flow

Single Processor Group `CRM Dynamics Discovery`:

1. `ExecuteStreamCommand` — runs the script every 30 minutes
2. `SplitJson ($.*)`
3. `EvaluateJsonPath` — extracts `data_type` to FlowFile attribute
4. `RouteOnAttribute` — 14 routes by `data_type`
5. 14× `PutDatabaseRecord` — Statement Type = UPSERT; same `JsonTreeReader` with `crm-dynamics-discovery.json` schema

## Avro Schema

`datalake/SQL/json_schemas/CRM/crm-dynamics-discovery.json`  
Record name: `CrmDynamicsDiscovery` (namespace `datalake.crm`)  
All 14 entity field sets as `["null", <type>]` unions in one record.

## Customer Identity Resolution

CRM account IDs are mapped to canonical platform customer keys via `discovery_crm_customer_alias`.

- Seeded by `SQL/CRM/seed_customer_alias_from_accounts.sql` (auto, best-effort name match)
- Corrected via GUI Settings → Customer Alias (promotes to `source = 'manual'`)
- Decision documented in ADR-0008

## GUI Consumption

| Feature | Source |
|---|---|
| Customer View → Sales tab | `customer-api /customers/{name}/sales/*` |
| DC Sales Potential page | `datacenter-api /datacenters/{dc}/sales-potential` |
| Settings → Customer Alias | `customer-api /crm/aliases` |

## Unit Tests

```bash
cd datalake/collectors/CRM/Dynamics365
python3 -m pytest test_crm_dynamics_discovery.py -v
# 43 tests + 14 subtests
```

## References

- Script README: `datalake/collectors/CRM/Dynamics365/README.md`
- ADR-0008: `adrs/ADR-0008-crm-customer-identity-resolution.md`
- Reference: ServiceCore ITSM collector (`datalake/collectors/ServiceCore/`)
