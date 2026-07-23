---
name: Send an externally-processed invoice to AppZen for audit
description: Push an invoice already processed in your own system into AppZen for validation checks and audit, then sync status.
api: openapi/appzen-autonomous-ap-openapi-original.yml
operations:
  - POST /invoice/externally-processed
  - POST /invoice/externally-processed-for-audit
  - GET /invoice/processed-invoice/{uuid}
  - POST /invoice/{uuid}/status-sync
---

# Send an externally-processed invoice to AppZen for audit

Use this flow when your ERP has already captured/processed an invoice and you want AppZen to run validation checks and/or a full audit against it.

## Authentication
Headers on every request: `x-api-key`, `customer-id`, `customer-key`.

## Steps
1. `POST /invoice/externally-processed` to create an externally processed invoice with optional validation checks, OR `POST /invoice/externally-processed-for-audit` to create it with full validation checks/audit. The response returns the invoice `uuid`.
2. `GET /invoice/processed-invoice/{uuid}` to retrieve the processed invoice record.
3. `POST /invoice/{uuid}/status-sync` to push the external status of the invoice back into AppZen (keeps AppZen's view aligned with your system of record).

## Rules
- Log the `x-correlation-id` response header for support.
- On HTTP 429 (rate limit) back off and retry; limits are 1,000/sec and 300,000/day.
- Error envelope is custom `{timestamp, status, error, message}`; inspect `message` for the failure reason.
