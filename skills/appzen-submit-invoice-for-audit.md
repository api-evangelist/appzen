---
name: Submit an invoice to AppZen for automated audit
description: Ingest an invoice into AppZen Autonomous AP and retrieve its AI audit results and risk score.
api: openapi/appzen-autonomous-ap-openapi-original.yml
operations:
  - POST /invoice
  - POST /invoice/search
  - GET /invoice/{uuid}
  - GET /invoice/{uuid}/audit-results
---

# Submit an invoice to AppZen for automated audit

Use this flow to push an invoice from your ERP/AP system into AppZen's Autonomous AP for automated processing, risk scoring, and audit.

## Authentication
Send all three API-key headers on every request (issued by AppZen Support):
- `x-api-key`
- `customer-id`
- `customer-key`

Base URL: `https://api.appzen.com/dictionary-data-services` (sandbox: `https://api.us.sandbox.appzen.com/dictionary-data-services`).

## Steps
1. Ensure referenced master data exists (supplier, entity, chart of accounts). See the `Sync AppZen master data` skill.
2. `POST /invoice` with the invoice payload (header, line items, supplier reference, PO reference for 2/3-way match). The response returns the invoice `uuid`.
3. To locate an existing invoice, `POST /invoice/search` with a query spec, or `GET /invoice/{uuid}`.
4. `GET /invoice/{uuid}/audit-results` to retrieve the audit findings and risk score once processing completes.

## Rules
- Rate limits: 1,000 req/sec, 300,000 req/day. Back off on HTTP 429.
- Capture the `x-correlation-id` response header and log it; quote it to AppZen Support for any troubleshooting.
- Errors return a custom JSON envelope `{timestamp, status, error, message}` (not RFC 9457) — read `message` for the cause and the embedded `Error code`.
- No idempotency-key is supported; de-duplicate on your side before re-POSTing.
