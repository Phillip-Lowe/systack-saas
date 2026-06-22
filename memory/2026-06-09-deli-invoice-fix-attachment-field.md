# Session — 2026-06-09 17:36 CDT
## Utopia Deli Invoice Pipeline Fix

**Status:** 🔧 IN PROGRESS — Attachment field name fixed

### Problem Found
IMAP trigger was downloading attachments but storing them in `$binary.attachment_` instead of `$binary.attachment_0`.

**Fix:** Changed **Attachment Field Name** from `attachment_` to `attachment_0`

### What Changed
- **Before:** `$binary.attachment_` (wrong name)
- **After:** `$binary.attachment_0` (matches workflow)

### Current Workflow Config
- **IMAP Trigger:** `downloadAttachments: true`, field name: `attachment_0`
- **If Node:** Checks `$binary.attachment_0.fileName` ends with `.pdf`
- **HTTP Request:** Sends `$binary.attachment_0` as multipart to `http://127.0.0.1:9002/extract`
- **Database:** Postgres `utopia_deli`

### Next Step
User will test with a real PDF email to verify the fix.

### Files
- Workflow: Utopia Deli Invoice Email Pipeline
- API: `deli_invoice_api.py` (port 9002)
- Database: Postgres `utopia_deli`
