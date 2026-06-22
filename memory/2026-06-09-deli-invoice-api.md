# Session — 2026-06-09 17:15 CDT
## Utopia Deli Invoice API Setup

**Status:** ✅ COMPLETE — Deli Invoice API created and tested

### What Was Built

**New API Endpoint:** `http://127.0.0.1:9002/extract`
- File: `deli_invoice_api.py`
- Port: 9002
- Database: Postgres `utopia_deli`
- User: `systack` / `Systack2026!CRM`

### Database Setup

**Database recreated:** `utopia_deli` (was deleted during June 8 cleanup)

**Tables created:**
- `deli_invoices` — Main invoice data (with JSONB items column)
- `deli_invoice_items` — Line items linked by invoice_id

**Schema:** `public`

### For n8n Workflow

**In your "Utopia Deli — Invoice Email Pipeline" workflow:**

Update the HTTP Request node:
- **Old:** `http://127.0.0.1:9001/extract`
- **New:** `http://127.0.0.1:9002/extract`

Everything else stays the same — same multipart form data, same response format.

### Postgres Node Config (if needed separately)

| Field | Value |
|-------|-------|
| Host | localhost |
| Port | 5432 |
| Database | utopia_deli |
| User | systack |
| Password | Systack2026!CRM |
| SSL | Disabled |

**Table:** `public.deli_invoices`

### Verification

Health check endpoint confirmed working:
```json
{
  "status": "ok",
  "service": "deli-invoice-extractor",
  "database": "postgres",
  "invoices_count": 0
}
```

### Key Points

1. **Keeps deli invoices separate** from Systack invoices (different database)
2. **Same parser** — uses `invoice_parser_production.py` for extraction
3. **Same API format** — multipart/form-data, field name: `invoice`
4. **No Postgres node needed** in n8n if using the API — it saves automatically

### Files
- `deli_invoice_api.py` — Deli invoice API server
- `utopia_deli` database — Postgres database for deli invoices

### Next Step
User will test the endpoint with the n8n workflow.
