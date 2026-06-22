# ASSEMBLY Pitfalls — Utopia Deli HTML Webhook Integration

## Date: 2026-06-02 / Updated 2026-06-03
## PLAN_ID: PLAN-HTML-WEBHOOK-INTEGRATION-2026-06-01
## ROLE: ASSEMBLY

### Pitfall 1: Reference Documents Not at Expected Paths
- **What:** Task specified `artifacts/n8n-workflow-field-map.md` and `artifacts/n8n-workflows/Order Received.json` but actual files were in `SOL n8n templates/` and workspace root.
- **Fix:** Used `find` to locate files. The actual reference workflows were in `SOL n8n templates/FIXED_*`.
- **Impact:** Would have failed to find reference patterns and built blindly.

### Pitfall 2: Response Node vs Immediate Response
- **What:** The webhook trigger can use `responseMode: "responseNode"` (waits for Respond to Webhook node) vs immediate response. For order flows, MUST use responseNode so validation and payment link generation complete before responding.
- **Fix:** Set `"responseMode": "responseNode"` in webhook trigger parameters. **CRITICAL: Remove `responseData` when using responseNode** — they conflict.
- **Impact:** Immediate response would return before order_id and payment_link are generated, breaking the confirmation page.

### Pitfall 3: Square Payment Link Line Items Format
- **What:** Square API expects `base_price_money.amount` in cents (integer), not dollars. Also expects `quantity` as string.
- **Fix:** Used `Math.round(item.price * 100)` to convert dollars to cents, and `String(item.qty)` for quantity.
- **Impact:** Square API rejects float amounts or numeric quantity types.

### Pitfall 4: Error Handling in n8n Webhooks
- **What:** If any code node throws, n8n returns a generic 500 unless you wire error outputs or use an error workflow.
- **Fix:** Used `settings.errorWorkflow` pointing to `Format Error Response` node. Also wrapped validations in code nodes that throw descriptive errors. **CRITICAL: Wire error outputs from EVERY node to the error handler** — n8n v1 won't auto-route errors to the error workflow node; you need explicit error connections.
- **Impact:** Without this, the HTML form would get raw n8n error HTML instead of clean JSON `{success: false, message: "..."}`.

### Pitfall 5: Tax Verification Tolerance
- **What:** JavaScript floating point math can cause `0.1 + 0.2 !== 0.3`. Tax calculation from cents-as-dollars can have tiny rounding differences.
- **Fix:** Used `.toFixed(2)` and allowed `0.02` tolerance in verification instead of exact equality.
- **Impact:** Without tolerance, legitimate orders could fail validation due to JS float rounding.

### Pitfall 6: Environment Variables for Secrets
- **What:** Square API token, location ID, and Google Sheets credentials must NOT be hardcoded in workflow JSON.
- **Fix:** Used `$env.SQUARE_ACCESS_TOKEN`, `$env.SQUARE_LOCATION_ID`, `$env.ORDERS_SHEET_ID` with placeholder credential ID.
- **Impact:** Hardcoded secrets would leak in version control and require workflow rebuilds to rotate.

### Pitfall 7: Hours Gate Uses Pickup Time, Not Just Current Time
- **What:** The HTML form sends a `pickup_time` field (e.g., "11:30"). The hours gate must validate THAT time, not just "are we open right now."
- **Fix:** Parsed `pickup_time` and validated it against configured hours (10:30 - 15:00 CT). If "ASAP", uses current time.
- **Impact:** Would have incorrectly rejected future orders placed during open hours.

### Pitfall 8: Google Sheets Credential Placeholder
- **What:** The workflow JSON needs actual Google Sheets OAuth2 credential ID to work, not just a placeholder.
- **Fix:** Added `"credentials": { "googleSheetsOAuth2Api": { "id": "REPLACE_WITH_CREDENTIAL_ID", ... }}` with a clear comment marker.
- **Impact:** Workflow won't log to Sheets until credential ID is replaced during n8n import.

### Pitfall 9: Google Sheets Node v1 vs v2 Syntax
- **What:** Modern n8n uses Google Sheets node v2 which requires `documentId`, `sheetName`, and `columns.mappingMode: "defineBelow"` with explicit `value` mappings. The v1 syntax (`sheetId` + `range`) fails silently on import or throws "Sheet not found" errors.
- **Fix:** Upgraded to v2 syntax with full column mapping:
  ```json
  {
    "documentId": { "__rl": true, "value": "={{$env.ORDERS_SHEET_ID}}", "mode": "list" },
    "sheetName": { "__rl": true, "value": "Orders", "mode": "list" },
    "columns": {
      "mappingMode": "defineBelow",
      "value": { "order_id": "=...", "customer_name": "=...", ... },
      "matchingColumns": ["submitted_at"]
    }
  }
  ```
- **Impact:** Workflow would fail to append rows to Sheets, breaking order logging.

## Lessons Learned
1. Always `find` files before assuming paths from task descriptions
2. Use `responseNode` mode when the response depends on downstream processing
3. **Never mix `responseData` with `responseMode: "responseNode"`**
4. Convert dollar amounts to cents before calling Square API
5. Add error handling from day one — wire error outputs explicitly
6. Allow small floating-point tolerance in monetary validation
7. Use `$env.*` for all secrets and service credentials
8. Validate the pickup_time, not just current time, for hours gate
9. Mark placeholder values clearly for the person importing the workflow
10. **Use Google Sheets v2 syntax** — documentId + sheetName + column mapping

## Files Created
- `/Users/philliplowe/.openclaw/workspaces/sol/utopia-deli-revamp/utopia-deli-html-order-v1.json` — Complete n8n webhook workflow JSON (v1.0.2)
- `/Users/philliplowe/.openclaw/workspaces/sol/utopia-deli-revamp/WEBHOOK-DOCS.md` — Full API documentation

### Pitfall 10: Deployed but Failing Workflows Return HTTP 200 with Empty Body
- **What:** When `responseNode` mode is used but execution errors before `Respond to Webhook`, n8n returns HTTP 200 with empty body (content-length: 0).
- **Why:** The webhook trigger accepted the request and started execution. The error happens downstream. n8n doesn't know to return an error HTTP status because the error workflow/node isn't wired to override the response.
- **Fix:** Ensure error handler also calls `Respond to Webhook` with appropriate error status code (400). Verify error outputs are wired FROM every node TO the error response handler.
- **Impact:** HTML form shows success (HTTP 200) but gets empty response, causing confusion. Customer thinks order went through but actually failed.
- **Evidence:** Fleet Canon workflow at `utopia-api.systack.net/webhook/utopia-deli-html-order` returns 200 with empty body but execution logs show `"Credential with ID 'tImuO0HlDDvXCT8w' does not exist"` error.

## Deployment Status (Updated 2026-06-07)
- **Build:** ✅ Complete (v1.0.2)
- **Import into n8n:** ✅ Done (imported via CLI)
- **Webhook registered:** ✅ Active at `utopia-api.systack.net/webhook/utopia-deli-html-order-v1`
- **Execution:** ❌ Fails — workflow validation issues with n8n 2.20.7-exp
- **Fleet Canon adapter:** Active but FAILING — missing Square credential
- **Credential placeholder:** `REPLACE_WITH_CREDENTIAL_ID` needs actual Google Sheets OAuth2 ID
- **Env vars:** Need verification on `utopia-api.systack.net`
- **Form integration:** ✅ Ready — CODY fixed schema mismatch

### Pitfall 11: n8n Webhook Path Duplication on Import
- **What:** Setting explicit `webhookId` on webhook node with `typeVersion: 1` causes n8n 2.20.7-exp to register path as `path/name/path` (e.g., `utopia-deli-html-order-v1/html%20order%20webhook/utopia-deli-html-order-v1`)
- **Fix:** Use `typeVersion: 1.1` (matching other working workflows) and remove explicit `webhookId`
- **Impact:** Webhook returns 404 "not registered" despite workflow being active. Took multiple restarts to diagnose.
- **Evidence:** Webhook entity showed mangled path in database until fixed.

### Pitfall 12: Workflow Validation Errors on Execution
- **What:** After fixing webhook path, execution fails with generic "workflow has issues" error. No specific node highlighted.
- **Hypothesis:** Node type versions in workflow JSON (built for older n8n) are incompatible with 2.20.7-exp. Code nodes use ES6 features (const, let, arrow functions, spread operator) that may fail in sandbox.
- **Status:** Unresolved — needs UI inspection or manual node-by-node testing.