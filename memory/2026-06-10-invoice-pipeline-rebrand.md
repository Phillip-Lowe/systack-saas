# 2026-06-10 — Invoice Pipeline Rebrand + Per-Invoice Summary Email

## Changes Made

### 1. Rebranded from "Deli" to Generic Invoice Pipeline
- Database renamed from `utopia_deli` → `invoice_pipeline`
- Tables renamed from `deli_invoices`/`deli_invoice_items` → `invoices`/`invoice_items`
- API docstring updated
- Server class renamed from `DeliInvoiceHandler` → `InvoicePipelineHandler`
- Print messages updated

### 2. Email Format Changed: Per-Invoice Summary
**Before:** Monthly aggregate (top vendors, recent invoices)
**Now:** Individual invoice breakdown with line items

**New email includes:**
- Vendor, Invoice #, Date, Total
- **Line item table** — name, qty, price, line total
- Subtotal, Tax, Total
- This month's running totals

### 3. New API Response Fields
```json
{
  "vendor": "Supplies, LLC",
  "invoice_number": "INV-2026-0612-001",
  "total": 2132.13,
  "items": [...],
  "postgres_id": 127,
  "email_subject": "Invoice Collected: Supplies, LLC — INV-2026-0612-001",
  "email_html": "<html>...invoice details...</html>"
}
```

## n8n Workflow (No Cron Needed)
- **Subject:** `{{ $json.email_subject }}`
- **Body (HTML):** `{{ $json.email_html }}`
- Fires **every time an invoice is received** (not batched)

## Business Direction
- No longer scoped to Utopia Deli specifically
- Generic system ready to deploy for any business
- Potential clients: businesses that receive vendor invoices via email (not Square users)

## Files Updated
- `deli_invoice_api.py` — Full rewrite with new schema and email format

## Database Migration Required
```sql
-- Run this in Postgres to migrate existing data
-- (or just create new tables)
CREATE TABLE IF NOT EXISTS invoices (
    id SERIAL PRIMARY KEY,
    file_name TEXT,
    vendor TEXT,
    invoice_date TEXT,
    invoice_number TEXT,
    subtotal NUMERIC(10,2),
    tax NUMERIC(10,2),
    total NUMERIC(10,2),
    raw_text TEXT,
    parsed_json JSONB,
    items JSONB,
    source TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE IF NOT EXISTS invoice_items (
    id SERIAL PRIMARY KEY,
    invoice_id INTEGER REFERENCES invoices(id),
    item_name TEXT,
    price NUMERIC(10,2),
    quantity INTEGER
);
```
