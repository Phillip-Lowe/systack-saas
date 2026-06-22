# Shared Learning Dump

## ASSEMBLY Session — 2026-06-07 (Webhook Deployment)

### What Got Fixed
1. **Imported workflow into n8n**: `utopia-deli-revamp/utopia-deli-html-order-v1.json` now loaded in database.
2. **Fixed webhook path duplication**: Removed explicit `webhookId`, upgraded `typeVersion` from 1 to 1.1.
3. **Webhook is live**: `POST https://utopia-api.systack.net/webhook/utopia-deli-html-order-v1` is registered and responding.

### New Pitfalls Documented
- **ASSEMBLY-011**: Webhook path duplication on import (typeVersion 1 + explicit webhookId)
- **ASSEMBLY-012**: Workflow validation errors — execution fails with generic "has issues" message. Likely node version incompatibility.

### Status
- Webhook endpoint: ✅ Active
- Workflow execution: ❌ Blocked by validation errors
- Next: Debug node compatibility with n8n 2.20.7-exp

---

## CODY Session — 2026-06-07 (HTML Webhook Integration)

### What Got Fixed
1. **HTML field name alignment**: Changed `name="notes"` → `name="special_instructions"` in `index.html` to match what webhook expects.
2. **Hours display text**: Updated hero hours text from "10:30 AM – 3:00 PM" → "12:30 PM – 7:30 PM" to match actual business hours.
3. **JavaScript payload schema**: Completely rebuilt `handleCheckout` to send flat fields matching WEBHOOK-DOCS.md v1.0.2:
   - `customer_name`, `email`, `phone` (top-level, not nested under `customer`)
   - `order_items` with `item_id`, `name`, `qty`, `price` (in dollars)
   - `subtotal`, `tax`, `total` (in dollars, not cents)
   - `pickup_time` as "HH:MM" string or "ASAP"
   - `special_instructions`, `source`, `timestamp`
4. **Fixed `finalPickupTime` undefined bug**: Now reads directly from DOM with `document.querySelector('select[name="pickup_time"]').value`
5. **Added ASAP option**: Populate pickup times with "ASAP" as first option per webhook docs.
6. **Added non-JSON response handling**: Check Content-Type before parsing, fallback to text.
7. **Added debug logging**: `console.log('Webhook payload:', payload)` for troubleshooting.
8. **Centralized TAX_RATE constant**: `const TAX_RATE = 0.0952` — avoids drift.

### New Pitfalls Documented
- **CODY-016**: HTML form field name mismatch (notes vs special_instructions)
- **CODY-017**: Undefined variable in payload (finalPickupTime)
- **CODY-018**: Nested vs flat payload structure mismatch
- **CODY-019**: Price unit confusion (cents internally, dollars in payload)

### Files Modified
- `utopia-deli-revamp/index.html` — field name + hours text
- `utopia-deli-revamp/order-form.js` — complete rebuild of handleCheckout, fixed schema
- `memory/agent-learnings/CODY-PITFALLS.md` — added CODY-016 through CODY-019

### Webhook Endpoint Confirmed
```
POST https://utopia-api.systack.net/webhook/utopia-deli-html-order-v1
Content-Type: application/json
```

### Testing Recommended
Run the curl test from WEBHOOK-DOCS.md to verify the endpoint is live, then test via the form in browser.
