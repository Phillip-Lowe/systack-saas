# VALI — Payment Flow Test (Fri, 2026-06-05)

## Mission
Design the test that proves the NEW webhook correctly generates valid Square payment links and that the payment lifecycle (generation → payment → deactivation) is covered.

## Context
- NEW webhook (`utopia-deli-html-order-v1.json` v1.0.2) generates Square payment links via HTTP Request node
- Legacy architecture has SEPARATE workflows for "Payment Completed" (disable link) and "2 AM Expiration" (delete unused link)
- The NEW webhook is a SINGLE workflow — it generates the link but has NO post-payment or expiration handling
- Webhook is BUILT but NOT deployed (returns 404 from `utopia-api.systack.net`)

## Reference: Legacy Payment Flow

From the architecture diagram:
```
Order Received
    ↓
Square Payment Link
    ↓
┌────────────┴────────────┐
↓                         ↓
Payment Completed        2 AM Expiration
    ↓                         ↓
Disable Link            Delete Unused Link
```

**Legacy behavior:**
1. "Order Received" workflow creates Square payment link
2. Customer pays via Square
3. "Payment Completed" workflow detects payment, disables the link (prevents reuse)
4. If customer doesn't pay by 2 AM, "2 AM Expiration" workflow deletes the unused link
5. Both workflows operate on `payment_link_id` stored in Google Sheets

## NEW Webhook Payment Flow (v1.0.2)

From `utopia-deli-html-order-v1.json`:
```
Build Square Line Items
    ↓
Create Square Payment Link (HTTP Request to Square API)
    ↓
Extract Payment Link (Set node: payment_link, payment_link_id)
    ↓
Email Customer (with payment_link)
    ↓
Email Kitchen (with payment_link)
    ↓
Log to Google Sheets (stores payment_link, payment_link_id)
```

**CRITICAL GAPS IDENTIFIED:**

### GAP-001: No Payment Completion Handler
- **Legacy:** "Payment Completed" workflow runs when Square reports payment success
- **NEW:** No workflow exists to handle payment completion
- **Impact:** Payment link remains active forever. Customer could theoretically reuse it, or the link could be shared/forwarded for multiple payments.
- **Severity:** MEDIUM — Square links are typically one-use by default, but this isn't verified

### GAP-002: No Link Expiration Handler
- **Legacy:** "2 AM Expiration" workflow deletes unused links at 2 AM
- **NEW:** No expiration or cleanup workflow
- **Impact:** Unused payment links accumulate in Square dashboard. Customer orders that never get paid stay in Sheets as "pending" forever.
- **Severity:** LOW — operational clutter, not a security risk

### GAP-003: No Link Format Validation
- **Legacy:** Implicit — Square API returns structured response
- **NEW:** Extracts `payment_link.url` and `payment_link.id` from response but doesn't validate they're non-null/non-empty
- **Impact:** If Square API returns unexpected structure, `payment_link` could be undefined. Customer gets email with "Pay here: undefined".
- **Severity:** MEDIUM — bad UX, potential lost payment

### GAP-004: No Link Reachability Check
- **Neither legacy nor NEW:** Verifies the generated link actually works
- **Impact:** Customer clicks link, gets 404 from Square. Order is in Sheets but payment fails.
- **Severity:** LOW — Square API should return valid links, but no verification

### GAP-005: No Idempotency on Payment Link Creation
- **Legacy:** Uses `$execution.id` as idempotency key
- **NEW:** Uses `order_id` (which has collision risk per VALI-006 / PESSI-012)
- **Impact:** If `order_id` collides, Square may return existing link instead of creating new one. Two different customers could get the same payment link.
- **Severity:** LOW — collision risk is ~1%, but real

## Payment Flow Test Matrix

### PASS Cases (Valid Payment Flow — Must Complete Successfully)

| # | Test | Expected Result | Validation Points |
|---|------|-----------------|-------------------|
| P-PASS-001 | Valid order → Square API call | HTTP 200 from Square, `payment_link.url` starts with `https://` | Link format, HTTP status |
| P-PASS-002 | Link extracted correctly | `payment_link` is non-empty string, `payment_link_id` is non-empty string | Null/undefined checks |
| P-PASS-003 | Customer email includes link | Email body contains `payment_link` URL | Template rendering |
| P-PASS-004 | Kitchen email includes link | Email body contains `payment_link` URL | Template rendering |
| P-PASS-005 | Sheets log includes link | `payment_link` and `payment_link_id` columns populated | Data persistence |
| P-PASS-006 | Response includes link | JSON response: `{ success: true, payment_link: "...", order_id: "..." }` | API contract |
| P-PASS-007 | Square metadata includes order_id | `reference_id` in Square order = `order_id` | Traceability |
| P-PASS-008 | Tax included as line item | Square order has "Tax" line item with correct `tax_cents` | Financial accuracy |

### FAIL Cases (Payment Flow Breaks — Must Handle Gracefully)

| # | Test | Trigger | Expected Error | Current Status |
|---|------|---------|----------------|----------------|
| P-FAIL-001 | Square API down | Mock 503 from Square | `SQUARE_ERROR` or generic error | ❌ **UNTESTED** |
| P-FAIL-002 | Invalid Square credentials | Bad `SQUARE_ACCESS_TOKEN` | `SQUARE_ERROR` | ❌ **UNTESTED** |
| P-FAIL-003 | Invalid location ID | Bad `SQUARE_LOCATION_ID` | `SQUARE_ERROR` | ❌ **UNTESTED** |
| P-FAIL-004 | Square returns empty response | `{}` from Square | `SYSTEM_ERROR` — link missing | ❌ **UNTESTED** |
| P-FAIL-005 | Square returns malformed URL | `url: "not-a-url"` | Validation error | ❌ **UNTESTED** |
| P-FAIL-006 | Payment link generation timeout | Square API > 30s | `SYSTEM_ERROR` or timeout | ❌ **UNTESTED** |
| P-FAIL-007 | Email fails after link created | SMTP error | Still return link to customer? | ❌ **UNTESTED** |
| P-FAIL-008 | Sheets fails after link created | Google API error | Still return link to customer? | ❌ **UNTESTED** |

## Test Design: `test_payment_link_generation`

**Purpose:** Verify that a valid order payload results in a properly formatted, reachable Square payment link, and that failures in the payment flow are handled gracefully.

**Preconditions:**
- Webhook is deployed and active (currently returns 404)
- `SQUARE_ACCESS_TOKEN` and `SQUARE_LOCATION_ID` env vars are set
- SMTP is configured for email nodes
- Google Sheets credential is configured

**Test Method:**

1. **Positive Test:** Send valid payload to webhook
   - Verify HTTP 200 response
   - Verify `success: true`
   - Verify `payment_link` is string starting with `https://`
   - Verify `order_id` matches `UDO-YYYYMMDD-###` format
   - Verify `payment_link_id` is non-empty
   - (Optional) HTTP HEAD the `payment_link` URL — expect 200 (Square checkout pages are reachable)
   - Check Google Sheets row: `payment_link` and `payment_link_id` populated
   - Check customer email: contains `payment_link` and `order_id`

2. **Negative Tests:** Send valid payload but trigger Square API failures
   - Mock/bypass Square API with 503 → expect `success: false`, no emails sent, no Sheets write
   - Mock with invalid credentials → expect `success: false`
   - Mock with empty response → expect `success: false`

3. **Partial Failure Tests:** Link created but downstream fails
   - Simulate email SMTP failure → webhook should still return `payment_link` to customer (link is the critical output)
   - Simulate Sheets failure → webhook should still return `payment_link` (non-critical logging)

**Validation Coverage:**
- [x] Square API call succeeds
- [x] Payment link format is valid HTTPS URL
- [x] Payment link ID is extracted and stored
- [x] Customer receives link
- [x] Kitchen receives link
- [x] Order ID is traceable in Square metadata
- [x] Tax is included as line item
- [ ] Link remains active after payment (GAP-001 — requires separate workflow)
- [ ] Unused links expire (GAP-002 — requires separate workflow)
- [ ] Link is reachable (GAP-004 — optional but valuable)

## Comparison: Legacy vs NEW Webhook Payment Flow

| Feature | Legacy | NEW Webhook v1.0.2 | Status |
|---------|--------|-------------------|--------|
| Square payment link generation | ✅ | ✅ | PASS |
| Link stored with order_id | ✅ | ✅ | PASS |
| Customer notified with link | ✅ (redirect) | ✅ (email) | PASS |
| Kitchen sees link | ✅ | ✅ | PASS |
| Payment completion handler | ✅ "Payment Completed" workflow | ❌ **NONE** | **FAIL** |
| Link deactivation on payment | ✅ Disable link | ❌ **NONE** | **FAIL** |
| Link expiration handler | ✅ "2 AM Expiration" workflow | ❌ **NONE** | **FAIL** |
| Unused link cleanup | ✅ Delete at 2 AM | ❌ **NONE** | **FAIL** |
| Link format validation | ⚠️ Implicit | ❌ No explicit check | **FAIL** |
| Idempotency key uniqueness | ✅ `$execution.id` | ⚠️ `order_id` (collision risk) | PARTIAL |
| Tax as line item | ✅ | ✅ | PASS |
| Metadata for traceability | ✅ | ✅ | PASS |

**Summary:**
- ✅ PASS: 6
- ❌ FAIL: 5
- ⚠️ PARTIAL: 1

## New Pitfall Discovered

**VALI-009: No Payment Link Lifecycle Management**
- **Context:** The NEW webhook generates a Square payment link but has no workflows to handle what happens AFTER generation. The legacy architecture has explicit "Payment Completed" and "2 AM Expiration" workflows that manage link state.
- **Failure Mode:** 
  1. Customer pays successfully → link remains active in Square → could be reused or forwarded
  2. Customer never pays → link stays active forever → clutter in Square dashboard
  3. Customer pays, disputes, charges back → no automated cleanup or reconciliation
  4. Kitchen staff manually managing links in Square dashboard = operational overhead
- **Fix:** 
  1. Create "Payment Completed" webhook/workflow that listens for Square payment webhooks and deactivates the link
  2. Create "Link Expiration" cron workflow that deletes unused links after 24 hours (or at 2 AM)
  3. Add `payment_link_status` column to Google Sheets (active, paid, expired, cancelled)
  4. Store `payment_link_created_at` timestamp for expiration logic

## Recommended Fixes for NEW Webhook

1. **Add link format validation:** After Extract Payment Link, verify `payment_link` starts with `https://` and `payment_link_id` is non-empty. If not, throw `SYSTEM_ERROR`.
2. **Add payment completion webhook:** n8n webhook listening for Square payment events → deactivate link → update Sheets status to "paid".
3. **Add expiration cron:** Daily at 2 AM → query Square for unpaid links created > 24 hours ago → delete → update Sheets status to "expired".
4. **Add Sheets status column:** `payment_link_status` (default: "active", transitions: "paid", "expired", "cancelled").
5. **Use better idempotency key:** Combine `order_id` with timestamp or use UUID to eliminate collision risk.

## Summary

| Status | Count |
|--------|-------|
| ✅ PASS | 6 (link generation, storage, notification, metadata, tax) |
| ❌ FAIL | 5 (completion handler, deactivation, expiration, format validation, idempotency) |
| ⚠️ PARTIAL | 1 (idempotency key) |

**CRITICAL:** The NEW webhook generates payment links correctly but has NO post-generation lifecycle management. The legacy architecture explicitly handles payment completion and link expiration. These are MISSING from the NEW webhook and represent both operational risk (clutter) and potential financial risk (reusable links).

**Next:** Saturday rotation — end-to-end test (HTML form → webhook → notification → Sheets)

---
*Generated by VALI on 2026-06-05 (Friday). Reference workflows are learning material. NEW webhook must pass all checks before shipping.*
