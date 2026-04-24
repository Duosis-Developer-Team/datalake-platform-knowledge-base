# ADR-0010 — CRM discovery: realized sales only (narrow scope)

**Status:** Accepted  
**Date:** 2026-04-24  
**Deciders:** Platform team  

## Context

The Dynamics 365 app registration used for datalake integration lacks privileges on several
entities (`invoice`, `contract`, and related line items). Attempts to read those endpoints
return **403 Forbidden** and pollute logs.

Business need for the platform GUI is:

- **Customers** (accounts) and identity resolution (ADR-0008).
- **Product catalog** (products, price lists, unit prices) for valuation.
- **Realized sales** only: sales orders in **Fulfilled** or **Invoiced** state with line items
  (`salesorderdetails`), not the full opportunity → quote → order → invoice pipeline.

## Decision

1. **Collector scope** — `crm-dynamics-discovery.py` fetches only:
   - Active accounts (`statecode eq 0`).
   - Active products (`statecode eq 0`).
   - Price lists and product price levels (full snapshot).
   - Sales orders matching `(statecode eq 3 or statecode eq 4)` with `$expand=order_details(...)`.

2. **No NiFi / DB tables** for: `opportunities`, `opportunityproducts`, `quotes`, `quotedetails`,
   `invoices`, `invoicedetails`, `contracts`, `contractdetails`. DDL for those tables is
   **archived** under `datalake/SQL/CRM/_archive_deprecated_entities/`. Existing deployments
   apply `datalake/SQL/CRM/migrations/2026-04-drop-non-realized-crm-tables.sql`.

3. **GUI and APIs** — Revenue and line-item reporting uses `discovery_crm_salesorders` and
   `discovery_crm_salesorderdetails` joined to catalog and customer alias tables.

## Consequences

### Positive

- Fewer Dynamics privileges required; fewer 403 errors.
- Simpler NiFi routing (6 `data_type` routes).
- Single source of truth for “what was sold” aligned to order fulfilment state.

### Negative / accepted risks

- **YTD revenue** in the GUI is based on **order** totals / line amounts, not posted invoices.
  Finance reconciliation may still require CRM invoice reports outside this platform.
- **Pipeline / open opportunity** metrics are **not** available from this collector; if needed,
  a separate privileged integration or manual export must be used.

## Alternatives rejected

| Alternative | Reason rejected |
|-------------|-----------------|
| Grant full invoice/contract read to the app | Security / least-privilege; not required for stated GUI use cases |
| Keep empty tables and silent 403 skips | Confusing schema and dead API code paths |

## References

- Collector: `datalake/collectors/CRM/Dynamics365/crm-dynamics-discovery.py`
- Customer identity: ADR-0008
- Migration: `datalake/SQL/CRM/migrations/2026-04-drop-non-realized-crm-tables.sql`
