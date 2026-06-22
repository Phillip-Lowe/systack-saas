# 2026-06-10 — Systack Invoice API on Port 9001

## Changes
- Created `systack_invoice_api.py` — dedicated Systack invoice pipeline API
- Runs on port 9001 (separate from generic invoice API on 8765 and deli/invoice_pipeline on 9002)
- Uses existing SQLite database `invoice_data.db`

## Features
- **Endpoint:** `POST /extract` — multipart form data with PDF invoice
- **Health:** `GET /health` — shows invoice count
- **Email summary:** Per-invoice HTML with line items, subtotal, tax, total
- **Monthly running total:** Included in each email

## API Response Fields
```json
{
  "vendor": "Supplies, LLC",
  "invoice_number": "INV-2026-0612-001",
  "total": 2132.13,
  "items": [...],
  "sqlite_id": 96,
  "email_subject": "Invoice Collected: Supplies, LLC — INV-2026-0612-001",
  "email_html": "<html>...invoice breakdown...</html>"
}
```

## n8n Workflow
- HTTP Request node: `http://127.0.0.1:9001/extract`
- Email node: `{{ $json.email_subject }}` / `{{ $json.email_html }}`

## Status
✅ Running on port 9001
✅ 95 invoices already in database
✅ Health check confirms: `systack-invoice-pipeline` service
