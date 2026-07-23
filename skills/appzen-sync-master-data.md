---
name: Sync AppZen master data
description: Upsert the reference data (suppliers, entities, chart of accounts, tax, VAT, payment terms) that invoices and purchase orders depend on.
api: openapi/appzen-autonomous-ap-openapi-original.yml
operations:
  - POST /supplier
  - POST /entity
  - POST /chart-of-accounts
  - POST /purchase-order
  - POST /tax
  - POST /vat
  - POST /payment-term
---

# Sync AppZen master data

Invoices and purchase orders in AppZen reference master-data records. Upsert them first so 2/3-way matching and audit have the data they need.

## Authentication
Headers on every request: `x-api-key`, `customer-id`, `customer-key`.

## Steps
1. `POST /supplier` to create/update suppliers (addressable later by `uuid` or `external-supplier-id`).
2. `POST /entity` to create/update legal/business entities.
3. `POST /chart-of-accounts` to sync the chart of accounts.
4. `POST /tax`, `POST /vat`, `POST /payment-term` to sync tax, VAT and payment-term reference data.
5. `POST /purchase-order` to load purchase orders that invoices will be matched against.
6. Verify any record with its `GET /{resource}/{uuid}` (or the external-id lookup) endpoint.

## Rules
- Each master-data POST is a create-or-update (upsert) keyed on the external id.
- Respect rate limits (1,000/sec, 300,000/day); handle HTTP 429 with backoff.
- Persist the `x-correlation-id` from each response for support and reconciliation.
