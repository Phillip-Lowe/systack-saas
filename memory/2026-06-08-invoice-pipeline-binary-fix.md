# 2026-06-08 — Invoice Pipeline Binary Fix Complete

## Status: ✅ WORKFLOW REDESIGNED FOR BINARY DATA

### Problem Found
The original workflow tried to pass PDF binary data through JSON (`$json.pdf_binary`), which doesn't work in n8n:
1. IMAP trigger with `downloadAttachments: true` stores files in `$binary.attachment_0`, not `$json.attachments`
2. Code node tried to extract `pdf.content` into JSON — binary can't be serialized
3. HTTP Request node tried to send JSON text as multipart form-data

### Fix Applied
Redesigned workflow to use n8n's native binary data handling:

**New Node Flow:**
1. **Invoice Email Trigger** (IMAP) → downloads attachments to `$binary.attachment_0`
2. **Move Binary Data** → renames `attachment_0` → `invoice_pdf`
3. **Has PDF Attachment?** → checks `$binary.invoice_pdf.fileName` ends with `.pdf`
4. **Call Invoice Parser** → sends `$binary.invoice_pdf` as multipart/form-data to `localhost:9001/extract`
5. **Log to Postgres** → logs extraction results with filename
6. **Email Notify Owner** → notification with vendor, amount, invoice #
7. **Skip Non-PDF** → handles non-PDF attachments

**Key Changes:**
- Removed: Extract PDF (Code node)
- Removed: Save PDF (Write Binary File node) — not needed since API handles PDF directly
- Added: Move Binary Data node (proper n8n binary handling)
- Fixed: HTTP Request now uses `parameterType: formBinaryData` with `$binary.invoice_pdf`
- Fixed: URL changed from `localhost:8000/extract-invoice` → `localhost:9001/extract`

### Current State
- **Workflow ID:** `Ny4kzzf1bN4NODGn`
- **Active:** ✅ True (in database)
- **Nodes:** 7 (properly configured)
- **Import status:** Successfully imported via n8n CLI
- **Published:** Yes (via `n8n publish:workflow`)

### What Happens Now
When an email arrives at `support@systack.net` with a PDF attachment:
1. IMAP trigger fires and downloads the PDF to `$binary.attachment_0`
2. Move Binary Data renames it to `$binary.invoice_pdf`
3. If filename ends with `.pdf`, proceeds to HTTP Request
4. HTTP Request sends the PDF as multipart/form-data to `localhost:9001/extract`
5. Parser API returns structured JSON with vendor, items, totals
6. Results logged to Postgres database
7. Email notification sent to `plowe@systack.net`

### Next Steps
1. Send a real test email with PDF attachment to `support@systack.net`
2. Wait for n8n to poll (IMAP checks every minute or so)
3. Check n8n executions for success/error
4. Verify Postgres has new invoice record
5. Check email inbox for notification

### Testing Command
```bash
# Send test PDF to IMAP account (requires SMTP credentials)
# Or use web interface to manually trigger
```

### Files
- `/tmp/invoice-pipeline-clean.json` — Corrected workflow JSON
- `/tmp/verify-workflow.json` — Exported verification

---

## Task Status Update

| Task | Status |
|------|--------|
| Fix Email Binary Pass-Through | ✅ COMPLETE |
| Build Web Dashboard | NOT STARTED |
| Find 3 Beta Testers | NOT STARTED |
| Add QuickBooks/Xero Export | NOT STARTED |
| Set Up Stripe Billing | NOT STARTED |

**Next:** Need to send test email with PDF to verify full pipeline works end-to-end.
