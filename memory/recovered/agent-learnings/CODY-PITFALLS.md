# CODY Pitfalls

## HTML/Webhook Integration

### Field Naming
- **Pitfall:** Using camelCase (`customerName`) instead of snake_case (`customer_name`) — n8n expects snake_case.
- **Fix:** Always match the exact field names from the n8n workflow JSON.

### Schema Mismatch Between Form and Webhook (PESSI-016)
- **Pitfall:** Form sends `quantity` but webhook validates `qty`; form calculates in cents but webhook expects dollars.
- **Impact:** Every real order fails validation or produces incorrect data.
- **Fix:** Read the webhook's Code node validation logic carefully. Match field names EXACTLY. Convert cents → dollars before POSTing if webhook expects dollars.

### CODY-011: Tax Rate Mismatch (2026-06-05)
- **Pitfall:** Form uses 9.5% tax rate but Arkansas state rate is 9.52%. Webhook validates against 9.52%.
- **Impact:** Tax calculation mismatch causes total validation failure in webhook.
- **Fix:** Always use exact tax rate from business requirements. For Utopia Deli: `TAX_RATE = 0.0952`.

### CODY-012: Pickup Time Format Mismatch (2026-06-05)
- **Pitfall:** Form sends ISO timestamp (`2026-06-05T15:30:00.000Z`) but webhook expects `"HH:MM"` or `"ASAP"`.
- **Impact:** Hours gate fails to parse pickup time, order rejected as outside business hours.
- **Fix:** Send time in `HH:MM` format (e.g., `"14:30"`) or `"ASAP"`. Never send ISO timestamps for time-only fields.

### CODY-013: Business Hours Mismatch (2026-06-05)
- **Pitfall:** Form shows 10 AM–8 PM but actual deli hours are 12:30 PM–7:30 PM Mon–Sat, closed Sunday.
- **Impact:** Customer selects invalid times, orders rejected by webhook hours gate.
- **Fix:** Match form hours to actual business hours. For Utopia Deli: open 12:30, close 19:30, skip Sunday.

### CODY-014: Menu Items Don't Match Catalog (2026-06-05)
- **Pitfall:** Form has generic items ("Philly Cheesesteak Sub") but Square catalog has specific items ("Cowboy Chik'n Sandwich", SKU_COWBOY).
- **Impact:** Payment link generation fails — Square doesn't recognize the item name.
- **Fix:** Match form menu exactly to Square catalog/SKU mapping. Include item_id that maps to Square catalog_object_id.

### Content-Type Header
- **Pitfall:** Sending `FormData` or `x-www-form-urlencoded` to n8n webhook.
- **Fix:** Always set `headers: {'Content-Type': 'application/json'}` when POSTing JSON.

### CORS
- **Pitfall:** Browser blocks fetch to different domain without CORS headers on the webhook.
- **Fix:** n8n webhook must have "Respond to Webhook" node or CORS middleware. For now, assume webhook accepts cross-origin.

### Phone Validation
- **Pitfall:** Accepting any string for phone number.
- **Fix:** Strip non-digits, validate length (10+ digits), format for display.

### Pickup Time Validation
- **Pitfall:** Allowing orders outside business hours.
- **Fix:** Validate pickup time is within operating hours and at least 15-30 min from now.

### Empty Cart Submission
- **Pitfall:** Submitting order with no items.
- **Fix:** Disable submit button until cart has items, validate before fetch.

### Error Handling
- **Pitfall:** Not handling network errors or non-200 responses.
- **Fix:** Always wrap fetch in try/catch, check `response.ok`, show user-friendly messages.

### Price Calculation
- **Pitfall:** Floating point math errors (`0.1 + 0.2 !== 0.3`).
- **Fix:** Use integer cents for calculations, format to dollars for display.

### Re-reading Form Values After DOM Changes
- **Pitfall:** Storing phone/email in variables but still reading from FormData object which has stale values.
- **Fix:** After normalizing/validating input, use the normalized variables in the payload, not `form.get()`.

### CODY-015: Webhook Response Parsing (2026-06-05)
- **Pitfall:** Assuming webhook always returns JSON. Some errors return plain text or HTML.
- **Impact:** `res.json()` throws, error handling fails, user sees confusing message.
- **Fix:** Check `Content-Type` header before parsing. Fall back to `res.text()` for non-JSON responses.

### CODY-016: HTML Form Field Name Mismatch (2026-06-07)
- **Pitfall:** HTML form uses `name="notes"` but JS reads `form.get('special_instructions')`. Field names must match.
- **Impact:** `special_instructions` is always empty string, webhook receives blank notes.
- **Fix:** Always align HTML `name` attributes with what JavaScript reads. When changing one, update the other.

### CODY-017: Undefined Variable in Payload (2026-06-07)
- **Pitfall:** JavaScript references `finalPickupTime` which was never declared. Script throws ReferenceError.
- **Impact:** Checkout fails silently, submit button spins forever, user sees no error.
- **Fix:** Always declare variables before use. Read pickup time from DOM: `const pickupTime = document.querySelector(...).value`.

### CODY-018: Nested vs Flat Payload (2026-06-07)
- **Pitfall:** Sending nested structure `{body: {customer: {name: ...}, items: [...]}}` when webhook expects flat `{customer_name: ..., order_items: [...]}`.
- **Impact:** Webhook validation fails — required fields missing at top level.
- **Fix:** Match the webhook docs exactly. Read WEBHOOK-DOCS.md schema before building payload.

### CODY-019: Price Unit Confusion (2026-06-07)
- **Pitfall:** Internal calculations in cents, payload sends cents to webhook that expects dollars.
- **Impact:** Webhook thinks $1300.00 instead of $13.00, total validation fails.
- **Fix:** Keep internal in cents for precision. Convert to dollars with `toFixed(2)` in payload. Webhook docs specify dollars.
