# 2026-06-04 — Utopia Deli Email Workflow Fix Session

## What We Were Fixing
The Utopia Deli HTML Order v1 workflow was sending plain text emails instead of branded HTML emails with payment links. The user wanted emails that matched the design from the "Order Received" workflow.

## What Worked

### ✅ Database Direct Updates
- Successfully updated n8n workflow nodes via SQLite
- Fixed node connections programmatically
- Changed node parameters without UI access

### ✅ Node Version Management
- Learned to downgrade node typeVersions for compatibility
- Code nodes: v2 → v1
- RespondToWebhook: 1.5 → 1.2
- EmailSend: 2.1 → 2
- HTTPRequest: 4.3 → 4.1

### ✅ Connection Fixes
- Removed duplicate connection entries
- Fixed execution chain with PREP_RESPONSE node
- Bypassed disabled Log to Postgres
- Separated email from response path

### ✅ Code Node Patterns
- Removed all `$items()` cross-references
- Used `items[0].json` for clean data flow
- Added validation for order_items
- ES5-compatible syntax only

### ✅ Response Path Architecture
- PREP_RESPONSE node before RespondToWebhook
- Clean JSON response, not email output
- Continue On Fail for email node

## What Didn't Work

### ❌ n8n 1.100.2 Downgrade
- Database schema incompatible (TypeORM changes)
- `ProjectRelation` table columns caused SQL errors
- Could not run migrations
- Had to revert to 2.20.7-exp.0

### ❌ Manual Code Generation
- Multiple syntax errors in Code node sandbox
- HTML entity escaping issues (`&lt;` vs `<`)
- Unicode escape issues (`\u003e` vs `>`)
- Required simplest possible string concatenation

### ❌ Aider for This Task
- Not configured for quick n8n fixes
- Would need git repo + Ollama setup
- Overkill for database direct edits

## Key Lessons

### n8n Code Node Sandbox
- NO template literals (backticks)
- NO spread operators (`...`)
- NO arrow functions in callbacks
- NO escaped quotes in strings
- Use `var` + simple string concatenation

### RespondToWebhook Requirements
- Must have DIRECT execution lineage from Webhook
- Cannot depend on Email/HTTP nodes (messy output)
- Should receive clean JSON
- Don't use `JSON.stringify` in response body
- Real webhook call required for testing

### Database Compatibility
- n8n versions NOT backward compatible
- TypeORM entities change between versions
- SQLite schema can't be easily migrated down
- Sticking with 2.20.7-exp.0 is safest

## Current State

**n8n:** 2.20.7-exp.0 running ✅  
**Workflows:** All activated ✅  
**Utopia Deli HTML Order v1:** Fixed in database ✅

### Changes Applied:
1. Branded email HTML with logo, colors, order summary
2. Payment button linking to Square checkout
3. Clean response path (PREP_RESPONSE → Respond)
4. ES5-compatible Code node
5. No `$items()` cross-references
6. Node versions matched to n8n 2.20.7-exp

## Next Steps

1. Access n8n UI when possible
2. Verify workflow visually
3. Save in UI to finalize
4. Test with REAL webhook call (curl or frontend)
5. Verify email renders correctly
6. Check payment link works

## Files Modified

- `~/.n8n/database.sqlite` — workflow_entity table

## Testing Command

```bash
curl -X POST https://n8n.systack.net/webhook/utopia-deli-html-order-v1 \
  -H "Content-Type: application/json" \
  -d '{
    "customer_name": "Test User",
    "email": "test@example.com",
    "phone": "501-555-1234",
    "order_items": [{"name":"Test Sandwich","price":10.99,"qty":1}],
    "subtotal": 10.99,
    "tax": 1.05,
    "total": 12.04,
    "pickup_time": "12:00 PM"
  }'
```

## Related
- [MEMORY.md](/MEMORY.md) — System decisions
- [TOOLS.md](/TOOLS.md) — n8n MCP connection
- `memory/2026-06-04.md` — Raw session log