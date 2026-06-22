# 2026-06-03 Evening Fix — Postgres Node Data Flow

## Problem
- Postgres node in n8n returns `{"success": true}` regardless of query
- Cannot rely on it to pass data to downstream nodes
- `RETURNING *` doesn't help — node still only returns success status

## Solution
- Use `$items("NodeName")` in Code/Function nodes to access data from specific previous nodes
- Bypass nodes that don't pass through data properly

## Applied
- Updated Build Payment Email to use `$items("Normalize + Carry Data")[0].json`
- This gets the order data directly from the Function node that merges HTTP response with original order data

## Key Learning
- n8n nodes have different data pass-through behaviors
- HTTP Request: replaces data with response
- Postgres: returns `{"success": true}`
- Function: passes through whatever you return
- Always verify what each node outputs in execution logs

