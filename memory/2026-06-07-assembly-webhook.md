# 2026-06-07 — ASSEMBLY: Webhook Build Status

## Session Summary
Built and deployed the n8n webhook endpoint for Utopia Deli HTML order form.

## What Was Done

### 1. Pre-Flight Check
- Read `memory/agent-learnings/ASSEMBLY-PITFALLS.md` — 10 pitfalls documented
- Read `memory/shared-learning-dump.md` — CODY fixed HTML form schema on 2026-06-07
- Read `utopia-deli-revamp/utopia-deli-html-order-v1.json` — complete v1.0.2 workflow exists

### 2. Discovered Issue
The workflow JSON was complete but **NOT imported into n8n**. Webhook returned 404.

### 3. Deployment Steps
1. Stopped n8n (managed by launchd `com.n8n.plist`)
2. Imported workflow via n8n CLI: `n8n import:workflow`
3. Published workflow: `n8n publish:workflow --id=utopia-deli-html-order-v1`
4. Restarted n8n manually (without launchd to avoid conflicts)

### 4. Key Technical Fix
**Pitfall discovered:** Webhook node `typeVersion: 1` with explicit `webhookId` causes path duplication in n8n 2.20.7-exp.
- Broken path: `utopia-deli-html-order-v1/html%20order%20webhook/utopia-deli-html-order-v1`
- Fix: Use `typeVersion: 1.1` (matching working v4 workflow) and let n8n auto-generate webhookId

### 5. Current Status
| Component | Status |
|-----------|--------|
| Workflow JSON | ✅ Complete (v1.0.2) |
| Imported to n8n | ✅ Yes |
| Webhook registered | ✅ Yes |
| Webhook responding | ✅ HTTP 200 on OPTIONS, execution starts on POST |
| Execution | ❌ Fails — "workflow has issues" |

### 6. Execution Error
```
Problem with execution: The workflow has issues and cannot be executed for that reason.
```

This is a **workflow validation error**, not a webhook error. The nodes are all present but n8n rejects execution due to internal validation (likely node parameter compatibility with n8n 2.20.7-exp).

### 7. Testing Performed
- OPTIONS request: 204 (CORS preflight works)
- POST with valid payload: Returns 500 with execution error
- Webhook path verified in database: `utopia-deli-html-order-v1`

## New Pitfall Documented

**ASSEMBLY-011: n8n Webhook Path Duplication**
- **What:** Setting `webhookId` manually on webhook node with `typeVersion: 1` causes n8n to concatenate `path + name + path`
- **Fix:** Use `typeVersion: 1.1` (matching working workflows) without explicit `webhookId`
- **Impact:** Webhook returns 404 with "not registered" even though workflow is active

## Files
- `utopia-deli-revamp/utopia-deli-html-order-v1.json` — Complete workflow
- `utopia-deli-revamp/WEBHOOK-DOCS.md` — API documentation

## Next Steps
1. Fix workflow validation issues (likely node version compatibility)
2. Test end-to-end with Square payment link generation
3. Configure Google Sheets OAuth2 credential
4. Configure SMTP for email notifications
5. Test from actual HTML form
