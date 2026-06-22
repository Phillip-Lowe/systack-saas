# 2026-06-03 Morning Session — n8n Workflow Fix

## Problem
The "Utopia Deli HTML Order v1" workflow was broken by direct database edits.
- Orders were failing with "undefined" values in Postgres
- HTTP Request node was dropping original order data
- Multiple failed attempts to fix via Python/SQLite scripts

## Root Cause (Finally Identified)
The HTTP Request node (Create Payment Link) replaces ALL input data with its API response.
So `items[0].json` after HTTP node only contains `{ payment_link: { url: '...' } }`, NOT the original order data.

Previous attempts to fix:
- Set Order Data node → didn't help because HTTP still replaced everything
- Merge Order Data Function node → only received HTTP response, no original data
- Direct database edits → corrupted workflow, made things worse

## Solution
Use n8n's built-in **Merge node** with parallel branches:

```
GEN_ORDER ──┬──→ Create Payment Link → merge.input(0) [Square response]
            └──→ merge.input(1) [Original order data]
                        ↓
                    merge (combineByPosition)
                        ↓
                Log to Postgres → Success Response
```

The merge node with `combineBy: 'combineByPosition'` pairs items by index:
- Input 0: `{ payment_link: { url: '...' } }`
- Input 1: `{ order_id: 'UDO-...', customer_name: '...', ... }`
- Output: `{ payment_link: { url: '...' }, order_id: 'UDO-...', ... }`

## MCP Connection (Key Discovery)
User provided n8n MCP connection:
```json
{
  "mcpServers": {
    "n8n-mcp": {
      "type": "http",
      "url": "https://n8n.systack.net/mcp-server/http",
      "headers": {
        "Authorization": "Bearer eyJhbG...0ea4"
      }
    }
  }
}
```

**MCP Tools discovered:**
- `search_workflows` — find workflows
- `get_workflow_details` — get workflow structure
- `validate_workflow` — validate SDK code before updating
- `update_workflow` — update workflow via SDK (SAFE, not direct DB)
- `execute_workflow` — trigger execution
- `get_execution` — check execution results
- `search_executions` — list executions
- `get_sdk_reference` — SDK documentation

## Workflow Updated Successfully
Used MCP `update_workflow` to update workflow ID `1WEM4rZxjhhy7ooM`:
- Node count: 9
- URL: https://n8n.systack.net/workflow/1WEM4rZxjhhy7ooM
- Credentials need manual reconfiguration in UI

## Lessons Learned
1. **NEVER edit n8n SQLite directly** — it breaks cache/state
2. **HTTP Request node replaces input data** — must use merge or configure response options
3. **Use MCP for workflow updates** — proper API, safe, validates code
4. **Test with MCP `validate_workflow` first** — catches errors before deploying
5. **Parallel branches need merge nodes** — n8n doesn't automatically merge data

## Files Created
- `/Users/philliplowe/.openclaw/workspaces/sol/PLAN-RESTORE-AND-FIX.md` — full plan
- `/Users/philliplowe/.openclaw/workspaces/sol/utopia-deli-workflow-sdk.js` — first SDK attempt
- `/Users/philliplowe/.openclaw/workspaces/sol/utopia-deli-fixed.js` — corrected SDK code
- `/Users/philliplowe/.openclaw/workspaces/sol/utopia-deli-simple.js` — simplified SDK code (used for update)

## Next Steps (for user)
1. Open https://n8n.systack.net/workflow/1WEM4rZxjhhy7ooM
2. Reconfigure Square API bearer token credential
3. Save workflow
4. Test with real order

## Last Working Order
- Order ID: UDO-20260603-603
- Time: 2026-06-03 03:51 CDT
- Status: Before my corruption started

## MCP Auth Token
Token for n8n MCP: `eyJhbG...0ea4` (stored in user's system, not in this file for security)
