# 2026-06-05 03:18 CDT — V2 Still Empty After Refresh

## Problem
User refreshed n8n UI after initial fix but V2 workflow still appears empty.

## Root Cause Found
**Negative Y coordinates** in node positions: `[600, -100]`, `[800, -100]`, etc.

n8n UI canvas starts at (0,0). Nodes with negative Y coordinates render **off-screen above the visible area**. The UI appears "empty" but nodes are actually there — just invisible.

## Fix Applied

Shifted all Y positions by +300 to make them all positive:

**Before:**
```
Normalize HTML → CART_STATE: [600, -100]
Write CART_STATE → Sheets: [800, -100]
Build cart_html: [1000, -100]
Email Template: [1200, -100]
```

**After:**
```
Normalize HTML → CART_STATE: [600, 200]
Write CART_STATE → Sheets: [800, 200]
Build cart_html: [1000, 200]
Email Template: [1200, 200]
```

Also updated `workflow_history` so n8n executes the corrected version.

## Prevention
When deploying workflows via SQL, always ensure:
1. All node positions are positive (X ≥ 0, Y ≥ 0)
2. activeVersionId matches versionId
3. workflow_history updated with same content

## Status
✅ Fixed. Try refreshing n8n UI now.
