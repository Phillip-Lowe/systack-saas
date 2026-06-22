# 2026-06-10 — IMAP Invoice Pipeline Debug Complete (3-Layer Fix)

**Status:** ✅ RESOLVED — Full root cause analysis and production-safe fix applied

## Previous State
- `memory/2026-06-09-deli-invoice-fix-attachment-field.md` — Fixed `$binary.attachment_` → `$binary.attachment_0` (was only partial fix)

## What Actually Fixed It (3 Independent Failure Layers)

### Layer 1 — IMAP Not Extracting Attachments
**Symptom:** Emails had attachments but n8n returned no binary field
**Cause:** IMAP default parsing is shallow; nested/inline MIME structures not traversed
**Fix:** `"format": "resolved"` forces deeper MIME parsing

### Layer 2 — Email Construction Variability
**Symptom:** Same workflow, inconsistent results across senders
**Cause:** Different MIME structures (flat vs nested vs inline)
**Key insight:** IMAP returns MIME trees, not files — attachments must be parsed and classified

### Layer 3 — IF Node Logic Failure
**Symptom:** Attachment exists but routed to FALSE branch
**Cause #1:** Wrong key — `$binary.attachment_` instead of `$binary.attachment_0`
**Cause #2:** Filename string match — `"Phone bill .pdf"` (space before .pdf) fails `endsWith ".pdf"`

## Final Fixed State

### IMAP Node
```json
{
  "format": "resolved",
  "downloadAttachments": true
}
```

### IF Node (Production Safe)
**Recommended:** Check `$binary.attachment_0.mimeType` equals `application/pdf`
**Acceptable:** Check `$binary.attachment_0.fileExtension` equals `pdf`
**Fragile:** `$binary.attachment_0.fileName` (avoid — spacing issues)

## System Learnings
1. IMAP ≠ attachments — returns MIME trees, not files
2. Attachments can be nested, inline, or misclassified
3. Filename logic is unreliable — prefer `mimeType`
4. Binary keys are dynamic — don't assume only `attachment_0`
5. `"resolved"` mode is mandatory for production IMAP

## Known Future Break Point
Current system assumes single attachment named `attachment_0`. Will break with:
- Multiple attachments
- Forwarded threads
- Mixed file types

## Required Next Upgrade (Optional)
Add Code Node after IMAP to normalize attachments → one item per file:
```js
const items = [];
for (const item of $input.all()) {
  if (!item.binary) continue;
  for (const key of Object.keys(item.binary)) {
    items.push({
      json: item.json,
      binary: { file: item.binary[key] }
    });
  }
}
return items;
```
Then update IF node to check `$binary.file.mimeType`.

## Workflow JSON (Final State)
See user message 2026-06-10 for full node definitions and connections.
- IMAP credential: `xBT92arTjBY66ccE` ("Utopia Deli Gmail IMAP")
- SMTP credential: `U7QjoOL2sgu4KLs6` ("Support Systack SMTP account")

## Status
- Extraction path verified ✅
- MIME parsing fixed ✅
- IF logic corrected ✅
- Known edge cases identified ✅
