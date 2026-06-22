# 2026-06-07 — CODY Daily Learning: HTML Webhook Integration

## Session Summary
Built and fixed the HTML order form + JavaScript webhook integration for Utopia Deli. The existing code had critical schema mismatches that would cause every order to fail.

## Fixes Applied

### 1. HTML Field Names (index.html)
- Changed `name="notes"` → `name="special_instructions"` to match webhook expected field
- Updated hours display: "Mon–Sat · 12:30 PM – 7:30 PM CT" (was showing wrong hours)

### 2. JavaScript Payload Rebuild (order-form.js)
**Before (broken):**
- Sent nested structure: `{body: {customer: {name: ...}, items: [...]}}`
- Prices in cents sent to webhook
- `finalPickupTime` variable undefined → ReferenceError
- Pickup time options missing "ASAP"
- No non-JSON response handling

**After (fixed):**
- Flat fields matching WEBHOOK-DOCS.md exactly:
  ```json
  {
    "customer_name": "...",
    "email": "...",
    "phone": "...",
    "order_items": [{"item_id": "...", "name": "...", "qty": 1, "price": 13.00}],
    "subtotal": 13.00,
    "tax": 1.24,
    "total": 14.24,
    "pickup_time": "14:30",
    "special_instructions": "...",
    "source": "web",
    "timestamp": "2026-06-07T15:00:00.000Z"
  }
  ```
- Prices converted cents → dollars with `toFixed(2)` before POST
- Pickup time read directly from DOM
- "ASAP" added as first option in pickup time dropdown
- Non-JSON response fallback (check Content-Type, fall back to text)
- Debug console.log added for payload inspection

## New Pitfalls
- **CODY-016**: HTML form field name mismatch (notes vs special_instructions)
- **CODY-017**: Undefined variable in payload (finalPickupTime)
- **CODY-018**: Nested vs flat payload structure mismatch
- **CODY-019**: Price unit confusion (cents internally, dollars in payload)

## Files Modified
- `utopia-deli-revamp/index.html`
- `utopia-deli-revamp/order-form.js`
- `memory/agent-learnings/CODY-PITFALLS.md`
- `memory/shared-learning-dump.md`

## Next Steps
1. Test the form in a browser (open `utopia-deli-revamp/index.html`)
2. Verify webhook endpoint is live with curl test from WEBHOOK-DOCS.md
3. Test end-to-end: add items → checkout → confirm payload in n8n
