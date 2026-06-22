# NEW Webhook Validation Checklist — VALI
## Date: 2026-06-02 (Tuesday rotation: Design NEW webhook validation checklist)
## Agent: VALI
## Reference: Legacy per-stage workflows vs NEW all-at-once webhook

---

## Philosophy
The legacy workflows validate incrementally (one form stage at a time). The NEW webhook must validate EVERYTHING in a single POST before any external API calls, database writes, or email sends. This checklist maps every legacy validation to its NEW webhook equivalent.

**Status Key:**
- ✅ PASS — NEW webhook covers this validation
- ❌ FAIL — NEW webhook is MISSING or WEAKER
- ⚠️ PARTIAL — NEW webhook covers it but with caveats
- ❓ UNKNOWN — Can't verify without seeing the running webhook

---

## 1. JSON Structure Validation

| # | Check | Legacy (per-stage) | NEW Webhook (all-at-once) | Status | Notes |
|---|-------|-------------------|---------------------------|--------|-------|
| 1.1 | Required fields present | Form node enforces `required` attribute | Code node checks `customer_name`, `email`, `phone`, `order_items` | ⚠️ PARTIAL | Legacy also validates per form (Contact, Item, Cart). NEW checks only top-level fields at entry. |
| 1.2 | `customer_name` non-empty | Form node `required` | Checks truthiness | ✅ PASS | |
| 1.3 | `email` format | Form node `type=email` + Abstract API deliverability check | Regex `^[^\s@]+@[^\s@]+\.[^\s@]+$` | ❌ FAIL | NEW regex is weaker — accepts `a@b.c`, no domain validation, no disposable check. |
| 1.4 | `email` deliverability | Abstract API (`emailreputation.abstractapi.com`) | None | ❌ FAIL | No external API call in NEW webhook. |
| 1.5 | `phone` format | Form node pattern + Abstract API | Digit count only (10-11 digits after stripping non-digits) | ❌ FAIL | No area code validation, no carrier check, no VOIP detection. |
| 1.6 | `phone` valid US number | Abstract API (`phoneintelligence.abstractapi.com`) | None | ❌ FAIL | |
| 1.7 | `order_items` is non-empty array | Built into form flow (can't reach checkout without items) | Explicit `Array.isArray(body.order_items) && body.order_items.length > 0` | ✅ PASS | NEW is explicit and early. |
| 1.8 | Each item has `item_id` | N/A (legacy uses form options, not IDs) | Checks `item.item_id` exists | ✅ PASS | NEW introduces ID-based lookup. |
| 1.9 | Each item has `name` | Form option labels | Checks `item.name` exists | ✅ PASS | |
| 1.10 | Each item `qty` is number 1-99 | Form input `type=number` with min/max | Checks `typeof item.qty === 'number' && item.qty >= 1 && item.qty <= 99` | ✅ PASS | NEW validates on server. |
| 1.11 | Each item `price` is number >= 0 | Hardcoded in form options | Checks `typeof item.price === 'number'` but NO cross-check against menu | ❌ FAIL | **CRITICAL**: Client can send any price. See PESSI-013. |
| 1.12 | `subtotal` is number >= 0 | Computed by workflow | Checks `typeof body.subtotal === 'number' && body.subtotal >= 0` | ✅ PASS | |
| 1.13 | `tax` is number >= 0 | Computed by workflow | Checks `typeof body.tax === 'number' && body.tax >= 0` | ✅ PASS | |
| 1.14 | `total` is number >= 0 | Computed by workflow | Checks `typeof body.total === 'number' && body.total >= 0` | ✅ PASS | |
| 1.15 | `pickup_time` present | Form field | Optional in payload; defaults to 'ASAP' | ⚠️ PARTIAL | Legacy requires explicit selection. NEW allows omission. |
| 1.16 | `special_instructions` is string | Textarea | Not validated (optional field) | ⚠️ PARTIAL | Should check max length (e.g., 500 chars) to prevent abuse. |
| 1.17 | `source` field | N/A | `source: 'web'` hardcoded by client | ❓ UNKNOWN | Could be spoofed. Should be validated or removed. |
| 1.18 | `timestamp` field | N/A | Sent by client | ❓ UNKNOWN | Could be spoofed. Should be generated server-side. |

**Structure Section Summary:**
- ✅ PASS: 9
- ❌ FAIL: 5
- ⚠️ PARTIAL: 3
- ❓ UNKNOWN: 2

---

## 2. Business Hours Validation

| # | Check | Legacy (per-stage) | NEW Webhook (all-at-once) | Status | Notes |
|---|-------|-------------------|---------------------------|--------|-------|
| 2.1 | Current time within open hours | `Function — Check Open Hours` reads `Open_Hours` sheet, checks both day AND time | `Hours Gate` checks time only (10:30-15:00) | ❌ FAIL | **CRITICAL**: NEW webhook ignores day-of-week closure. See PESSI-011. |
| 2.2 | Day-of-week open/closed status | Reads `is_open` column from `Open_Hours` sheet | Hardcodes all days as open | ❌ FAIL | |
| 2.3 | Pickup time is in the future | Implicit (can't select past time in form) | No check — accepts any time string | ❌ FAIL | Customer could send `pickup_time: "08:00"` for tomorrow morning. Should verify it's a reasonable future window (e.g., within 7 days). |
| 2.4 | Hours read from config sheet | `Open_Hours!A:D` Google Sheet | Hardcoded JS object `HOURS` | ⚠️ PARTIAL | Legacy can update hours without code change. NEW requires redeploy. |
| 2.5 | Timezone handling | `America/Chicago` via `Intl.DateTimeFormat` | `America/Chicago` via `toLocaleString` | ✅ PASS | Both use same timezone. |
| 2.6 | `ASAP` handling | N/A (legacy has explicit time slots) | Converts ASAP to current time | ✅ PASS | NEW handles ASAP explicitly. |

**Hours Section Summary:**
- ✅ PASS: 2
- ❌ FAIL: 3
- ⚠️ PARTIAL: 1

---

## 3. Financial Validation

| # | Check | Legacy (per-stage) | NEW Webhook (all-at-once) | Status | Notes |
|---|-------|-------------------|---------------------------|--------|-------|
| 3.1 | `subtotal + tax === total` | `Validate Total` Code node (LATE in workflow, after API calls) | `Verify Totals` Code node (EARLY, before any APIs) | ✅ PASS | NEW is BETTER — validates before consuming resources. |
| 3.2 | Tax rate is 9.52% | Hardcoded in metadata (`TAX_RATE: 0.0952`) | Explicitly checks `tax ≈ subtotal * 0.0952` (±0.02) | ✅ PASS | NEW validates rate explicitly. |
| 3.3 | Rounding tolerance | N/A | Allows ±0.02 difference | ✅ PASS | Handles JS floating point errors. |
| 3.4 | Item prices match menu database | Implicit (form options have hardcoded prices) | NOT CHECKED — accepts client `item.price` blindly | ❌ FAIL | **CRITICAL**: See PESSI-013. Attack vector: modified client JS. |
| 3.5 | Line item total = qty × unit price | Implicit (computed from hardcoded prices) | NOT CHECKED | ❌ FAIL | NEW should verify `subtotal === Σ(qty × price)` for all items. |
| 3.6 | Negative values rejected | Form inputs prevent negative | Code checks `>= 0` for subtotal, tax, total | ✅ PASS | |
| 3.7 | Currency consistency | Hardcoded USD | Hardcoded USD in Square API call | ✅ PASS | |

**Financial Section Summary:**
- ✅ PASS: 4
- ❌ FAIL: 2
- ⚠️ PARTIAL: 0

---

## 4. Security & Abuse Prevention

| # | Check | Legacy (per-stage) | NEW Webhook (all-at-once) | Status | Notes |
|---|-------|-------------------|---------------------------|--------|-------|
| 4.1 | Rate limiting (per IP) | N/A (Google Forms handles this) | None | ❌ FAIL | **CRITICAL**: Open endpoint, no throttling. See PESSI-010. |
| 4.2 | Rate limiting (per email/phone) | N/A | None | ❌ FAIL | |
| 4.3 | XSS sanitization | None (raw injection into email HTML) | None (text emails only, but still injects raw strings) | ❌ FAIL | See PESSI-005. |
| 4.4 | Input length limits | Form `maxlength` attributes | None | ❌ FAIL | `customer_name`, `special_instructions` could be megabytes. |
| 4.5 | Shared secret / auth | `x-shared-secret` header check in GForms workflow | None | ❌ FAIL | NEW webhook is completely open. |
| 4.6 | Order ID uniqueness | Google Sheets `cart_id` (sequential) | `Math.random()` 3-digit sequence | ❌ FAIL | Collision risk ~1% after 30 days. See PESSI-012. |
| 4.7 | Idempotency key uniqueness | `$execution.id` (n8n execution UUID) | `order_id` (which has collision risk) | ❌ FAIL | Square idempotency key collision if `order_id` collides. |

**Security Section Summary:**
- ✅ PASS: 0
- ❌ FAIL: 7
- ⚠️ PARTIAL: 0

---

## 5. Data Integrity & Post-Processing

| # | Check | Legacy (per-stage) | NEW Webhook (all-at-once) | Status | Notes |
|---|-------|-------------------|---------------------------|--------|-------|
| 5.1 | Order ID format | `UDO-YYYYMMDD-###` | Same format `UDO-YYYYMMDD-###` | ✅ PASS | |
| 5.2 | Order ID generation | Sequential via Sheets | Random 3-digit | ❌ FAIL | Not guaranteed unique. |
| 5.3 | Payment link creation | Square API with `$execution.id` as idempotency key | Square API with `order_id` as idempotency key | ⚠️ PARTIAL | Collision risk propagates to Square. |
| 5.4 | Payment link TTL / expiration | None in legacy | None in NEW | ❌ FAIL | 2 AM expiration cron exists but not validated at creation time. See PESSI-006. |
| 5.5 | Google Sheets logging | `Orders!A:Z` append | Same | ✅ PASS | Both log to Sheets. |
| 5.6 | Sheets credential validation | Hardcoded credential ID | `REPLACE_WITH_CREDENTIAL_ID` placeholder | ❌ FAIL | See PESSI-008. |
| 5.7 | Customer email sent | Gmail node with HTML template | Email node with plain text | ⚠️ PARTIAL | CHATTY designed HTML email but not yet integrated into workflow JSON. |
| 5.8 | Kitchen email sent | N/A (separate workflow?) | Email node with plain text list | ✅ PASS | NEW sends kitchen email. |
| 5.9 | Email subject includes order ID | No | No | ❌ FAIL | Should include `order_id` for reference. |
| 5.10 | Response includes `order_id` | N/A (form redirect) | JSON response includes `order_id` | ✅ PASS | NEW returns structured data. |
| 5.11 | Response includes `payment_link` | N/A (form redirect) | JSON response includes `payment_link` | ✅ PASS | |
| 5.12 | Error response format | HTML error page | JSON `{success: false, message: "..."}` | ✅ PASS | NEW is better for API consumers. |

**Data Integrity Section Summary:**
- ✅ PASS: 5
- ❌ FAIL: 4
- ⚠️ PARTIAL: 2

---

## 6. Test Plan: What Must Be Tested Before Shipping

### 6.1 Positive Test Cases (Must All Pass)

| # | Test | Expected Result |
|---|------|---------------|
| P1 | Valid complete payload with 2 items, correct totals, valid hours | `success: true`, `order_id` generated, `payment_link` returned, emails sent, Sheets logged |
| P2 | Valid payload with `pickup_time: "ASAP"` | Converts to current time, passes hours gate |
| P3 | Valid payload with `special_instructions: ""` | Accepts, stores empty string |
| P4 | Valid payload with single item, qty=1 | Accepts, total = price + tax |
| P5 | Valid payload with modifiers array | Accepts, includes modifiers in kitchen email |

### 6.2 Negative Test Cases (Must All Fail with Correct Error)

| # | Test | Expected Error | HTTP Status |
|---|------|----------------|-------------|
| N1 | Missing `customer_name` | `"Missing required fields: customer_name"` | 400 |
| N2 | Missing `email` | `"Missing required fields: email"` | 400 |
| N3 | Invalid email format (`not-an-email`) | `"Invalid email format"` | 400 |
| N4 | Phone with 9 digits | `"Invalid phone number"` | 400 |
| N5 | Phone with 12 digits | `"Invalid phone number"` | 400 |
| N6 | Empty `order_items: []` | `"order_items must be a non-empty array"` | 400 |
| N7 | Item missing `item_id` | `"Each order item must have item_id, name, qty, and price"` | 400 |
| N8 | Item `qty: 0` | `"Item quantity must be between 1 and 99"` | 400 |
| N9 | Item `qty: 100` | `"Item quantity must be between 1 and 99"` | 400 |
| N10 | `subtotal` negative | `"subtotal must be a non-negative number"` | 400 |
| N11 | `subtotal + tax !== total` (off by $0.05) | `"Total mismatch: expected X, got Y"` | 400 |
| N12 | Tax rate wrong (e.g., 8% instead of 9.52%) | `"Tax mismatch: expected X (9.52%), got Y"` | 400 |
| N13 | Pickup time before open (e.g., `09:00`) | `"Pickup time 09:00 is outside business hours (10:30 - 15:00 CT)"` | 400 |
| N14 | Pickup time after close (e.g., `16:00`) | `"Pickup time 16:00 is outside business hours (10:30 - 15:00 CT)"` | 400 |
| N15 | `customer_name` with XSS `<script>alert(1)</script>` | Should sanitize OR reject | 400 or sanitized |
| N16 | `special_instructions` > 500 chars | Should reject or truncate | 400 |
| N17 | `item.price` modified to $0.01 (actual $13.00) | Should reject (menu price mismatch) | 400 |
| N18 | Duplicate rapid submissions (5 in 1 second from same IP) | Should rate-limit | 429 |
| N19 | Sunday order with Monday pickup_time | Should reject (day-of-week check) | 400 |
| N20 | Pickup time 1 week in future | Should reject (too far ahead) | 400 |

---

## 7. Gap Analysis: Where NEW Webhook Is WEAKER Than Legacy

| Gap | Severity | Owner | Fix Complexity |
|-----|----------|-------|---------------|
| No day-of-week hours check | **HIGH** | PESSI-011 | Medium — add day check to Hours Gate |
| No email deliverability check | MEDIUM | PESSI-003 | Low — add fallback regex, optional API |
| No phone validation beyond digit count | MEDIUM | PESSI-003 | Low — add libphonenumber or stricter regex |
| No menu price cross-check | **HIGH** | PESSI-013 | Medium — lookup item_id against menu DB |
| No rate limiting | **HIGH** | PESSI-010 | Medium — middleware or Cloudflare |
| No XSS sanitization | **HIGH** | PESSI-005 | Low — HTML-encode before email |
| No input length limits | MEDIUM | VALI-004 | Low — add maxLength checks |
| Math.random() order_id | MEDIUM | PESSI-012 | Low — use timestamp+counter or UUID |
| No shared secret auth | MEDIUM | VALI-005 | Low — add x-shared-secret check |
| No payment link TTL validation | LOW | PESSI-006 | Medium — add created_at timestamp check |
| `REPLACE_WITH_CREDENTIAL_ID` placeholder | MEDIUM | PESSI-008 | Low — replace with actual credential or env var |
| No `subtotal === Σ(qty × price)` check | **HIGH** | NEW | Low — single Code node addition |

---

## 8. Today's Single Test Design

**Test Name:** `test_payload_completeness_validation`

**Purpose:** Verify that the NEW webhook rejects incomplete or malformed payloads BEFORE any external API calls.

**Test Method:** Send 20 curated payloads (10 positive, 10 negative) to the webhook endpoint and verify:
1. All positive payloads return `success: true` with `order_id` and `payment_link`
2. All negative payloads return `success: false` with a descriptive `message`
3. No negative payload triggers a Square API call, email send, or Sheets write
4. Response time for negative payloads < 500ms (validation should be fast)

**Validation Coverage:**
- [x] Required fields (1.1)
- [x] Email format (1.3)
- [x] Phone format (1.5)
- [x] Order items structure (1.7-1.11)
- [x] Numeric fields (1.12-1.14)
- [x] Hours gate (2.1)
- [x] Totals verification (3.1)
- [x] Tax rate (3.2)

**Not Yet Covered (requires NEW webhook code changes):**
- [ ] Day-of-week check (2.2)
- [ ] Menu price cross-check (3.4)
- [ ] Subtotal = Σ(line items) (3.5)
- [ ] XSS sanitization (4.3)
- [ ] Rate limiting (4.1)
- [ ] Input length limits (4.4)

---

## 9. Next Steps for Other Agents

| Day | Agent | Task | What They Should Do |
|-----|-------|------|-------------------|
| Wed | VALI | Build payload completeness test | Create the 20 test payloads, automate the test runner |
| Thu | VALI | Build hours gate test | Test edge cases: Sunday, holidays, DST transitions, ASAP at 3:01 PM |
| Fri | VALI | Build payment flow test | Verify link generation → payment → deactivation chain |
| Sat | VALI | Build end-to-end test | HTML form → webhook → email → Sheets full flow |
| Sun | VALI | Full integration test plan | Combine all tests into a shipping checklist |

---

*Generated by VALI on 2026-06-02. Reference workflows are learning material. NEW webhook must pass all checks before shipping.*
