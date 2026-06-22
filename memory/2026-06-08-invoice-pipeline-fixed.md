# 2026-06-08 — Invoice Pipeline Activation (Round 2)

## Status: ✅ WORKFLOW FIXED AND RE-ACTIVATED

### Problem Found
The published version of the workflow had the IMAP trigger node with empty parameters (`{"options": {}}`), missing required fields:
- `mailbox` (INBOX)
- `postProcessAction` (read)
- `downloadAttachments` (true)
- `format` (simple)

Also:
- HTTP Request node had empty `bodyParameters` array
- Email Send node was missing `bodyFormat` and `body` fields

### Fix Applied
1. Updated workflow via n8n API PUT with corrected node parameters
2. Re-activated the workflow (published new version)
3. Version now: `0faf7820-0cf4-44d7-a847-059c910aa142`

### Current State
- **Workflow ID:** `Ny4kzzf1bN4NODGn`
- **Name:** Systack Private — Invoice Email Pipeline
- **Active:** ✅ True
- **Nodes:** 8 (all present and configured)
- **IMAP trigger:** Configured with INBOX, read mode, download attachments
- **Last error:** #418 (old, pre-fix, from 11:32:45 UTC)
- **Log confirms:** "Activated workflow" messages with no new errors

### What Happens Now
n8n is polling the SUPPORT Systack IMAP account (support@systack.net). When an email arrives:
1. IMAP trigger fires
2. Check for attachments
3. Extract PDF attachment
4. Save to `/data/invoices/`
5. Call parser API at `http://localhost:8000/extract-invoice`
6. Log to Postgres
7. Email notification to pLowe@systack.net

### Next Steps
1. Wait for a real email to arrive (or send a test PDF)
2. Monitor executions for success
3. Check parser API is running on localhost:8000

### Key Lesson
The n8n API PUT requires `settings: {}` (not with `executionOrder`/`binaryMode`). The published version (`activeVersion`) can differ from the draft (`nodes`) — always verify both after updates.
