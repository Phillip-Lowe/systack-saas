# 2026-06-05 03:21 CDT — "Add First Step" Error

## Problem
User sees "Add first step" message in n8n UI. Workflow appears completely empty.

## Root Cause Found
**Nodes were missing the `id` field.**

n8n requires each node to have a unique UUID `id`. Without it, the UI cannot render nodes and shows "Add first step" instead.

## Fix Applied

Added unique UUIDs to all 14 nodes:
```
Webhook Trigger: 29ebdb3c-6dac-4d3a-a119-5cdcc5707e48
Validate JSON: 3a7b2c1d-4e5f-6a7b-8c9d-0e1f2a3b4c5d
Valid?: 5c6d7e8f-9a0b-1c2d-3e4f-5a6b7c8d9e0f
Normalize HTML → CART_STATE: 7a8b9c0d-1e2f-3a4b-5c6d-7e8f9a0b1c2d
...etc...
```

Also updated webhook node typeVersion from `1` to `1.1`.

## Why Previous Fixes Didn't Work
1. activeVersionId fix — enabled version matching
2. Position fix — made nodes visible on canvas  
3. **But nodes still had no IDs → UI couldn't render them**

## Prevention
When deploying workflows via SQL, ALWAYS include:
```python
node['id'] = str(uuid.uuid4())  # Required!
node['position'] = [x, y]        # Must be positive
node['typeVersion'] = 1.1        # Use recent version
```

## Status
✅ All 14 nodes now have unique IDs. 
✅ Refresh n8n UI — workflow should display correctly.
