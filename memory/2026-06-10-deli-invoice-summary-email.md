# 2026-06-10 — Deli Invoice Email Summary

## What Changed
Modified `deli_invoice_api.py` to generate a monthly summary when each invoice is processed.

## Why
User requested: "Instead of a separate transfer script, include a running summary in the notification email when an invoice is collected — so the owner knows the current month's totals, vendor breakdown, and recent activity."

## Changes Made

### API Response Now Includes
```json
{
  "vendor": "Supplies, LLC",
  "invoice_number": "INV-2026-0612-001",
  "total": 2132.13,
  "postgres_id": 127,
  "email_subject": "Invoice Collected: Supplies, LLC — INV-2026-0612-001",
  "email_html": "<html>...monthly summary...</html>"
}
```

### New Functions Added to API
1. `get_monthly_summary()` — Queries Postgres for:
   - Current month total invoices and amount
   - Top 5 vendors by amount
   - Last 5 invoices collected

2. `format_email_summary(summary)` — Builds HTML email with:
   - Invoice just collected (vendor, number, amount)
   - Monthly totals
   - Vendor breakdown table
   - Recent invoices table

## n8n Workflow Update Needed
In the "Email Notify Owner" node:
- **Subject:** `{{ $json.email_subject }}`
- **Body (HTML):** `{{ $json.email_html }}`

## Example Email
Subject: `Invoice Collected: Supplies, LLC — INV-2026-0612-001`

Body:
```
📋 Invoice Summary — June 2026

Invoice Just Collected:
Vendor: Supplies, LLC
Invoice #: INV-2026-0612-001
Amount: $2,132.13

Monthly Totals:
Total Invoices Collected: 12
Total Amount: $8,450.23

Top Vendors:
Supplies, LLC     3 invoices    $4,200.00
Fresh Foods Co    4 invoices    $2,100.00
...

Recent Invoices:
Supplies, LLC    INV-2026-0612-001    $2,132.13    06/10
Fresh Foods Co   FFC-2026-0608       $540.00      06/08
...
```

## Files Updated
- `deli_invoice_api.py` — Added summary functions and included in response

## Wiki Entry
- `.openclaw/wiki/main/Deli-Invoice-Summary-Email.md`

## MEMORY.md Updated
- Added to IMAP invoice pipeline section
