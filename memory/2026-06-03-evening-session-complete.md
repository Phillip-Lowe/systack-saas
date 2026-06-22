# 2026-06-03 Evening Session — Complete Documentation

## Session Times
- **Started**: ~16:17 CDT
- **Human Away**: 20:10 CDT  
- **Status**: Work in progress, n8n restart pending

## The Problem
Utopia Deli order workflow fails with `EMAIL MISSING` or `PAYMENT URL MISSING` errors. Build Payment Email node receives wrong data from previous nodes.

## Root Cause #1: Postgres Node Data Pass-Through
- **Log to Postgres** node returns `{"success": true}` instead of order data
- Even with `RETURNING *`, Postgres node doesn't pass through original data
- Build Payment Email node receives `{"success": true}` instead of `email`, `payment_link_url`

## Root Cause #2: n8n Workflow Version Architecture
From n8n source code (`active-workflow-manager.ts` line ~500):
```typescript
const publishedData = await this.workflowPublishedDataService.getPublishedWorkflowData(
    initialWorkflowData.id,
);
const { nodes, connections } = publishedData.publishedVersion;
```

**Executions load from `workflow_history` via `workflow_published_version`, NOT from `workflow_entity`**

## Root Cause #3: HTTP Request Returns Buffer
- n8n 2.20.7 HTTP Request node returns gzip-compressed Buffer even with `responseFormat: "json"`
- Function node sandbox: `Buffer.from()` not available, can't parse gzip
- Square API response bytes start with `0x1f 0x8b` (gzip magic number)

## What I Tried (All Failed)
1. ✅ Added `RETURNING *` to Postgres query — still returns `{"success": true}`
2. ✅ Changed Build Payment Email to use `$items("Normalize + Carry Data")` — not available in Code node v2 sandbox
3. ✅ Changed to `$node["Normalize + Carry Data"]` — not available in sandbox
4. ✅ Restructured flow connections — bypassed Postgres, got correct email but payment URL still missing
5. ✅ Added Buffer parsing code — crashes because `Buffer.from()` not available in sandbox
6. ✅ Published via MCP — created history entries but DID NOT update `workflow_published_version` table
7. ✅ Direct SQLite edits to `workflow_entity` — ignored by execution engine

## The Architecture I Finally Understood

### Database Tables (from n8n entity definitions)
1. **`workflow_entity`** — stores current DRAFT (UI only), has `versionId` and `activeVersionId`
2. **`workflow_history`** — stores SAVED versions, actual execution data loaded from here
3. **`workflow_published_version`** — maps `workflowId` → `publishedVersionId` (FK to `workflow_history.versionId`)

### What Actually Matters for Execution
- `workflow_published_version.publishedVersionId` determines which history entry runs
- My edits to `workflow_entity` were completely ignored

### Current State (Fixed Before Restart)
- `workflow_published_version.publishedVersionId` = `2e82cf8b-00f8-461e-9feb-9f4251a5b357` ✅
- `workflow_history` entry updated with safe Buffer handling ✅
- Build Payment Email node updated to handle missing payment URL gracefully ✅

## The Restart
- `secureCookie` already `false` in `~/.n8n/config`
- n8n PID: 54337 (main), 54356 (task-runner)
- Kill command: `kill 54337` (should auto-restart via launch agent)
- After restart: n8n will load from `workflow_history` via `workflow_published_version`

## Remaining Questions
1. Will HTTP Request node still return Buffer after restart?
   - If YES: email sends without payment URL (graceful fallback)
   - If NO: email includes payment URL (full functionality)
2. Will `$items()` or `$node[]` work in Function node after restart?
   - Probably still NO (sandbox limitation, not cache issue)

## Citations
- **n8n source**: `packages/cli/src/active-workflow-manager.ts` — execution loading
- **n8n source**: `packages/@n8n/db/src/entities/workflow-published-version.ts` — entity definition  
- **n8n source**: `packages/@n8n/db/src/entities/workflow-history.ts` — entity definition
- **GitHub Issue #24418**: https://github.com/n8n-io/n8n/issues/24418 — cache not clearing
- **GitHub Issue #25071**: https://github.com/n8n-io/n8n/issues/25071 — cached versions persisting

## Files Created
- `/Users/philliplowe/.openclaw/workspaces/sol/memory/2026-06-03-afternoon-session.md`
- `/Users/philliplowe/.openclaw/workspaces/sol/memory/2026-06-03-afternoon-fix.md`
- `/Users/philliplowe/.openclaw/workspaces/sol/memory/2026-06-03-evening-debug.md`
- `/Users/philliplowe/.openclaw/workspaces/sol/memory/2026-06-03-postgres-fix.md`
- `/Users/philliplowe/.openclaw/workspaces/sol/memory/2026-06-03-live-fix-session.md`
- `/Users/philliplowe/.openclaw/workspaces/sol/memory/n8n-architecture-actual.md`
- `/Users/philliplowe/.openclaw/workspaces/sol/memory/n8n-architecture-citations.md`

## Next Steps (After Restart)
1. Test real order from frontend
2. Check execution logs for actual error
3. If HTTP Request still returns Buffer: need different approach (Function node with `httpRequest` helper, or n8n UI fix)
4. If payment URL works: celebrate! 🎉

## MCP Token Status
- Token saved in `/Users/philliplowe/.openclaw/workspaces/sol/.n8n_mcp_token`
- Last confirmed working: 2026-06-03 16:17 CDT
- May need refresh after restart

## n8n API Key Status
- Key saved in `/Users/philliplowe/.openclaw/workspaces/sol/.n8n_api_key`
- Last confirmed: 2026-06-03 16:17 CDT
- Status unknown after restart
