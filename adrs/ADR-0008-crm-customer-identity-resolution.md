# ADR-0008 — CRM Customer Identity Resolution

**Status:** Accepted  
**Date:** 2026-04-24  
**Deciders:** Platform team  

## Context

The Dynamics 365 CRM system uses a GUID-based `accountid` as the primary customer identifier. The Datalake-Platform GUI and all other collectors (NetBox, VMware, Nutanix) use a human-readable `canonical_customer_key` (e.g. account name or normalized short name). Additionally, the NetBox inventory uses a `custom_fields_musteri` text field to associate VMs with customers.

A mapping layer is required to correlate:

```
CRM accountid (GUID) ↔ canonical_customer_key (GUI selector) ↔ NetBox custom_fields_musteri
```

## Decision

We implement a **customer alias table** (`discovery_crm_customer_alias`) with the following design:

| Column | Role |
|---|---|
| `crm_accountid` | PK — CRM account GUID |
| `crm_account_name` | CRM account display name (at seed time) |
| `canonical_customer_key` | Platform-wide canonical key for GUI selectors and API filters |
| `netbox_musteri_value` | Value matching `discovery_netbox_virtualization_vm.custom_fields_musteri` |
| `source` | `auto` (seed script) or `manual` (operator-corrected) |

### Auto-seeding

`SQL/CRM/seed_customer_alias_from_accounts.sql` runs after each accounts discovery:

1. Inserts rows for all active CRM accounts (`statecode = 0`).
2. Sets `canonical_customer_key = account.name` (best guess).
3. Attempts a best-effort match to `discovery_netbox_virtualization_vm.custom_fields_musteri` using normalized lower-case string comparison.
4. Rows where `source = 'manual'` are protected — their `canonical_customer_key` and `netbox_musteri_value` are never overwritten by re-seeding.

### Operator refinement

Operators correct unmatched or mismatched rows via the GUI Settings → Customer Alias page, which issues `PUT /api/v1/crm/aliases/{crm_accountid}` (promotes the row to `source = 'manual'`).

### Valuation formula

The datacenter sales-potential value is computed as:

```
potential_value = Σ (idle_capacity_unit × catalog_price_per_unit)
```

Where:
- `idle_capacity_unit` = DC total capacity − customer-allocated capacity (from NetBox/VMware/Nutanix)
- `catalog_price_per_unit` = `discovery_crm_productpricelevels.amount` (TL price list)
- Join: `customerid → discovery_crm_customer_alias → netbox_musteri_value → VM inventory`

## Consequences

### Positive
- No change to existing collectors or CRM schema required.
- Operators can iteratively correct mappings without re-running any pipeline.
- All sales-related GUI features can be delivered incrementally as alias quality improves.

### Negative / Accepted risks
- Initial auto-seed accuracy depends on name similarity — expect ~60-80% auto-match rate.
- Operators must manually fix mismatches, especially for accounts with short or non-unique names.
- CRM account name changes after auto-seed are not automatically propagated to `canonical_customer_key` (operators must re-review).

## Alternatives Rejected

| Alternative | Reason rejected |
|---|---|
| Use CRM GUID directly everywhere | Would require migrating all existing GUI selectors and NetBox data |
| Sync CRM accountids to NetBox | Requires CRM write access; violates single-direction data flow |
| Manual-only mapping from day one | Unacceptably slow to bootstrap; auto-seed provides useful starting point |
