# Session — 2026-06-09 17:45 CDT
## Utopia Deli Invoice Pipeline — Attachment Not Recognized

**Status:** ❌ BLOCKED — IMAP PDF attachments not being recognized

### Problem Summary
Despite multiple configuration attempts, the IMAP trigger is not properly exposing PDF attachments to downstream nodes. The workflow keeps skipping PDFs and routing to "Skip Non-PDF".

### What Was Tried
1. ✅ Enabled `downloadAttachments: true`
2. ✅ Set `dataPropertyAttachmentsPrefixName: "attachment_0"`
3. ✅ Changed field name from `attachment_` to `attachment_0`
4. ✅ Verified IMAP options: mailbox=INBOX, postProcessAction=read, format=simple
5. ✅ Confirmed If node checks `$binary.attachment_0.fileName` ends with `.pdf`
6. ✅ Confirmed HTTP Request sends `formBinaryData` with `inputDataFieldName: "attachment_0"`

### Current Workflow Config
```json
{
  "downloadAttachments": true,
  "dataPropertyAttachmentsPrefixName": "attachment_0",
  "options": {
    "mailbox": "INBOX",
    "postProcessAction": "read",
    "format": "simple"
  }
}
```

### What We Know
- Email metadata comes through (from, to, subject, date)
- No `$binary` data visible in execution output
- PDF attachments are being skipped by If node
- Same workflow structure worked for Systack pipeline (port 9001)

### Differences from Working Systack Pipeline
- Systack uses credential `uZXvyt7Wd0RbQreY` (Gmail IMAP)
- Deli uses credential `xBT92arTjBY66ccE` (Gmail IMAP)
- Both use same IMAP trigger type (emailReadImap v2)
- Systack pipeline has Move Binary Data node between IMAP and If
- Deli pipeline goes direct IMAP → If

### Possible Causes (Not Verified)
1. **Gmail app password issue** — `theutopiadelilittlerock@gmail.com` app password may be revoked/broken
2. **Credential permissions** — Deli IMAP credential may lack attachment access
3. **n8n version bug** — `emailReadImap` v2 may have issues with certain Gmail accounts
4. **Missing Move Binary Data node** — Systack pipeline requires this node for attachments to work
5. **Attachment size/format** — PDF may be too large or not standard format
6. **Email client sending format** — iPhone Mail may encode attachments differently

### Next Steps (When User Returns)
1. Check if Systack pipeline still works with current n8n version
2. Try adding Move Binary Data node between IMAP and If
3. Test with different email client (not iPhone Mail)
4. Check n8n execution logs for IMAP errors
5. Verify Gmail app password for deli email is valid
6. Try recreating the IMAP credential
7. Check if `attachment_0` exists in `$binary` with JSON.stringify debug

### Files
- Workflow: Utopia Deli Invoice Email Pipeline
- API: `deli_invoice_api.py` (port 9002, running)
- Database: Postgres `utopia_deli` (ready)

### Session End
User had to leave. Problem unresolved. Will revisit later.
