# ADR-0009 – ServiceCore Customer Resolution via Email-Domain Chain (no DDL change)

| Field      | Value                              |
|------------|------------------------------------|
| Status     | Accepted                           |
| Date       | 2026-04-24                         |
| Deciders   | Platform team                      |
| Tags       | itsm, servicecore, customer, email |

## Context

The Datalake-Platform collects ServiceCore ITSM data into three silver-layer tables:

- `discovery_servicecore_users` — user directory (email, user_id, full_name)
- `discovery_servicecore_incidents` — incidents (org_user_id BIGINT FK → users.user_id)
- `discovery_servicecore_servicerequests` — service requests (requester_id BIGINT FK → users.user_id)

We need to present incident + SR data per customer on the Platform GUI's Customer View page.

### Problem

The GUI works with canonical customer names (e.g. "Boyner", "Türk Telekom") sourced from NetBox/CRM.  
There is no explicit foreign key from these customers to ServiceCore user accounts.

### Alternatives considered

1. **Add `email_domains[]` column to `discovery_crm_customer_alias`** — rejected because it requires a DDL migration on a production datalake table and adds a maintenance burden.

2. **String-match `org_users_name` / `requester_full_name`** — unreliable due to character encoding variations, partial names, and display-name changes over time.

3. **Email-domain ILIKE chain (chosen)** — no DDL changes; relies on the fact that ServiceCore user emails contain the company domain (e.g. `fatih.senel@boyner.com.tr`). Combined with the existing BIGINT `user_id` FK linkage documented in the DDL COMMENT, this gives reliable, indexed joins.

## Decision

Customer → ServiceCore ticket resolution is performed entirely at **query time** using:

1. **`customer_to_email_needle(name)`** — a Python helper that normalises the canonical customer name to a case-insensitive email domain fragment (Turkish character map + lower-case + noise collapse). Result is used as a PostgreSQL `ILIKE '%@%<needle>%'` filter.

2. **`customer_users` CTE** — selects `(user_id, full_name, email)` from `discovery_servicecore_users` where `email ILIKE :needle` and the user is active (`is_enabled=true`, `soft_deleted=false`).

3. **BIGINT JOIN** — `discovery_servicecore_incidents.org_user_id = customer_users.user_id` and `discovery_servicecore_servicerequests.requester_id = customer_users.user_id`. Both columns are indexed (`idx_discovery_servicecore_incidents_org_user_id`, `idx_discovery_servicecore_sr_requester_id`).

No changes are made to any datalake table schema.

## Consequences

### Positive

- Zero DDL / migration risk on production datalake tables.
- Leverages existing indexed BIGINT FKs — fast, join-safe.
- Robust to name casing, Turkish characters, and display-name variations.
- Single, composable CTE reused by `/itsm/summary`, `/itsm/extremes`, and `/itsm/tickets` endpoints.

### Negative / Trade-offs

- If a customer's email domain changes (e.g. acquisition / rebrand), the needle must be updated via the customer selector in the GUI (no automated sync).
- Companies sharing an email domain (rare edge case) would aggregate under the same needle — acceptable for operations dashboards.
- SR resolution time KPI cannot be computed because `discovery_servicecore_servicerequests` has no `closed_and_done_date` column. Avg/median/p95 resolution metrics cover **incidents only**.

## References

- `datalake/SQL/ServiceCore/discovery_servicecore_users.sql` (DDL COMMENT: *"Join user_id to incident org_user_id or service request requester_id"*)
- `Datalake-Platform-GUI/services/customer-api/app/utils/customer_needle.py`
- `Datalake-Platform-GUI/services/customer-api/app/db/queries/itsm.py`
- ADR-0008 – CRM Customer Identity Resolution
