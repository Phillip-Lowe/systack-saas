# VALI — Hours Gate Test (Thu, 2026-06-04)

## Mission
Design the test that proves the NEW webhook's hours gate correctly accepts valid pickup times and rejects invalid ones.

## Context
- NEW webhook (`utopia-deli-html-order-v1.json`) has a `Hours Gate` Code node
- Current implementation: checks time is between 10:30 and 15:00 CT, ignores day-of-week
- Form sends `pickup_time` as "HH:MM" string or "ASAP"
- Legacy workflows read `Open_Hours` Google Sheet with `is_open` per day
- Both use `America/Chicago` timezone

## Reference: Legacy Hours Validation

The legacy `Function — Check Open Hours` node:
1. Reads `Open_Hours!A:D` sheet (columns: day, open_time, close_time, is_open)
2. Gets current day-of-week (0=Sunday, 6=Saturday)
3. Looks up today's row
4. Checks `is_open` === true
5. If closed → returns "We're closed today"
6. If open → checks current time against open_time and close_time
7. Returns boolean + message

**Legacy behavior:**
- Sunday: Closed (is_open = false)
- Monday-Saturday: Open 10:30-15:00 (is_open = true)
- ASAP at 14:59 → OK
- ASAP at 15:01 → "Sorry, we're closed"
- Pickup time "09:00" → Rejected (before open)
- Pickup time "16:00" → Rejected (after close)

## NEW Webhook Hours Gate (Current v1.0.1)

From `utopia-deli-html-order-v1.json`:
```javascript
const HOURS = { open: "10:30", close: "15:00" };
const now = new Date().toLocaleString("en-US", { timeZone: "America/Chicago" });
const timeStr = now.split(", ")[1].slice(0, 5); // "HH:MM"

let pickupTime = body.pickup_time;
if (pickupTime === "ASAP") {
  pickupTime = timeStr;
}

const [pickupH, pickupM] = pickupTime.split(":").map(Number);
const [openH, openM] = HOURS.open.split(":").map(Number);
const [closeH, closeM] = HOURS.close.split(":").map(Number);

const pickupMinutes = pickupH * 60 + pickupM;
const openMinutes = openH * 60 + openM;
const closeMinutes = closeH * 60 + closeM;

if (pickupMinutes < openMinutes || pickupMinutes >= closeMinutes) {
  return [{ json: { error: `Pickup time ${pickupTime} is outside business hours (${HOURS.open} - ${HOURS.close} CT)` } }];
}
```

**CRITICAL BUGS IDENTIFIED:**

### BUG-001: No Day-of-Week Check
- **Attack:** Send order on Sunday with pickup_time "12:00"
- **Result:** Passes validation. Kitchen gets order on closed day.
- **Fix:** Add day-of-week lookup, reject if `is_open === false`

### BUG-002: "ASAP" After Close Passes with Wrong Time
- **Scenario:** Customer submits at 15:01 CT
- **Current:** ASAP converts to "15:01", which is >= 15:00 close time
- **Expected:** Should reject with "Sorry, we're closed"
- **Risk:** Low — form should block submit after hours, but API is open

### BUG-003: Future Pickup Times Not Validated
- **Attack:** Send `pickup_time: "12:00"` on Monday, but today is Tuesday
- **Wait:** The webhook treats "12:00" as a time, not a date. It doesn't know what day "12:00" refers to.
- **Question:** Is `pickup_time` meant to be today only? What if customer wants tomorrow?
- **Current behavior:** Any "HH:MM" is checked against today's hours, regardless of actual date intent
- **Risk:** MEDIUM — customer might expect to schedule tomorrow, webhook assumes today

### BUG-004: No "Lead Time" Buffer
- **Legacy:** Form enforces 20-minute lead time (can't select time < now+20min)
- **NEW:** No lead time check. ASAP is current time. "10:31" at 10:30 would pass.
- **Risk:** Kitchen gets order with 1 minute notice.

### BUG-005: DST Transition Edge Cases
- **Scenario:** Order during spring forward (2:00 AM → 3:00 AM)
- **Risk:** Time comparison might be off by 1 hour on DST transition days
- **Current:** Uses `toLocaleString` which handles DST, but 10:30 might be 11:30 effectively
- **Fix:** Use UTC for all comparisons, or use a proper timezone library

### BUG-006: Invalid Time Format Accepted
- **Attack:** `pickup_time: "25:00"` or `pickup_time: "not-a-time"`
- **Current:** `"25:00".split(":")` → `[25, 0]`, `25 * 60 + 0 = 1500`, which is >= 15:00 (900) → rejected
- **Actually:** `25 * 60 + 0 = 1500`, `closeMinutes = 15 * 60 + 0 = 900`, `1500 >= 900` → rejected. So it fails, but error message is misleading.
- **Risk:** UX issue — wrong error message

### BUG-007: ASAP During "Closed" Hours
- **Scenario:** Form somehow submits at 08:00 (before open)
- **Current:** ASAP → "08:00", which is < 10:30 → rejected. Good.
- **But:** No clear message telling customer "We're not open yet, try at 10:30"

## Hours Gate Test Matrix

### PASS Cases (Valid Pickup Times — Must Return 200)

| # | Test | Time | Day | Expected Result | Notes |
|---|------|------|-----|-----------------|-------|
| H-PASS-001 | ASAP during open hours | Current time | Tue-Fri | 200 OK | Normal case |
| H-PASS-002 | Specific time during open | "12:30" | Tue-Fri | 200 OK | Normal case |
| H-PASS-003 | ASAP at 10:30 exactly | "10:30" | Tue-Fri | 200 OK | Boundary: open time |
| H-PASS-004 | ASAP at 14:59 | "14:59" | Tue-Fri | 200 OK | Boundary: 1 min before close |
| H-PASS-005 | Specific time "10:31" | "10:31" | Wed | 200 OK | Just after open |

### FAIL Cases (Invalid Pickup Times — Must Return 400)

| # | Test | Time | Day | Expected Error | Current Status |
|---|------|------|-----|----------------|----------------|
| H-FAIL-001 | ASAP after close | "15:01" | Tue | "Outside business hours" | ✅ Likely passes (>= 15:00) |
| H-FAIL-002 | ASAP before open | "08:00" | Tue | "Outside business hours" | ✅ Passes (< 10:30) |
| H-FAIL-003 | Specific time after close | "16:00" | Tue | "Outside business hours" | ✅ Passes (>= 15:00) |
| H-FAIL-004 | Specific time before open | "09:00" | Tue | "Outside business hours" | ✅ Passes (< 10:30) |
| H-FAIL-005 | Sunday — any time | "12:00" | Sun | "We're closed today" | ❌ **FAILS** — no day check |
| H-FAIL-006 | Monday — any time | "12:00" | Mon | Depends on Open_Hours | ⚠️ UNKNOWN — check sheet |
| H-FAIL-007 | "ASAP" with no lead time | Current time | Any | "Need at least 20 min" | ❌ **FAILS** — no lead time check |
| H-FAIL-008 | Invalid time format | "25:00" | Any | "Invalid time format" | ⚠️ PARTIAL — wrong error |
| H-FAIL-009 | Non-numeric time | "lunch" | Any | "Invalid time format" | ⚠️ PARTIAL — wrong error |
| H-FAIL-010 | Empty time string | "" | Any | "Invalid time format" | ⚠️ PARTIAL — may crash |
| H-FAIL-011 | Future date implied | "12:00" (tomorrow) | Any | Clarify: date not supported? | ❌ **FAILS** — no date handling |
| H-FAIL-012 | DST spring forward day | "10:30" | DST day | Verify correct effective time | ❓ UNKNOWN — needs test |
| H-FAIL-013 | DST fall back day | "10:30" | DST day | Verify correct effective time | ❓ UNKNOWN — needs test |
| H-FAIL-014 | Holiday closure | "12:00" | Holiday | "We're closed today" | ❌ **FAILS** — no holiday check |

## Comparison: Legacy vs NEW Webhook Hours Validation

| Validation | Legacy (Per-Stage) | NEW Webhook (All-At-Once) | Status |
|------------|-------------------|---------------------------|--------|
| Time range (10:30-15:00) | ✅ | ✅ | PASS |
| Day-of-week open/closed | ✅ Open_Hours sheet | ❌ Hardcoded all days open | **FAIL** |
| ASAP handling | N/A (form only) | ✅ Converts to current time | PASS |
| Lead time buffer | ✅ 20 min in form | ❌ No check | **FAIL** |
| Invalid time format | ✅ Form dropdown | ⚠️ Misleading error | PARTIAL |
| DST handling | ✅ Browser handles | ⚠️ `toLocaleString` handles | PARTIAL |
| Holiday closures | ❌ Not supported | ❌ Not supported | GAP |
| Future date scheduling | ❌ Not supported | ❌ Not supported | GAP |

## New Pitfall Discovered

**VALI-007: Hours Gate Assumes `pickup_time` Is Always Today**
- **Context:** The webhook treats "HH:MM" as today's time. There's no date component.
- **Failure Mode:** A customer sending "12:00" on a Monday might intend Tuesday pickup. The webhook checks Monday's hours, not Tuesday's. If Monday is closed but Tuesday is open, the order is wrongly rejected.
- **Fix:** Either (a) add a `pickup_date` field, or (b) document that all orders are for same-day pickup only, or (c) reject orders submitted on closed days entirely.

**VALI-008: No Lead Time = Kitchen Rush Orders**
- **Context:** The legacy form enforces 20-minute lead time. The NEW webhook has no such check.
- **Failure Mode:** Customer submits at 14:59 for ASAP pickup. Kitchen has 1 minute to prepare. Or worse: submits at 10:30 for 10:30 pickup — impossible to prep.
- **Fix:** Add minimum lead time (e.g., 15-20 minutes) to ASAP orders. Specific times should be >= current time + lead time.

## Test Payloads for Automated Testing

```javascript
// H-PASS-001: ASAP during open hours (simulated)
{
  "customer_name": "Test User",
  "email": "test@example.com",
  "phone": "5015550000",
  "order_items": [{"item_id":"test","name":"Test","quantity":1,"unit_price_cents":1000,"line_total_cents":1000}],
  "subtotal_cents": 1000,
  "tax_cents": 95,
  "total_cents": 1095,
  "pickup_time": "ASAP"
}

// H-FAIL-005: Sunday order (simulated by setting server time to Sunday)
// This requires mocking the server date or testing on actual Sunday
{
  "customer_name": "Sunday Test",
  "email": "test@example.com",
  "phone": "5015550000",
  "order_items": [{"item_id":"test","name":"Test","quantity":1,"unit_price_cents":1000,"line_total_cents":1000}],
  "subtotal_cents": 1000,
  "tax_cents": 95,
  "total_cents": 1095,
  "pickup_time": "12:00"
}
// Expected: 400, "We're closed today (Sunday)"
```

## Recommended Fixes for NEW Webhook

1. **Add day-of-week check:** Read `Open_Hours` sheet or hardcode closure days
2. **Add lead time buffer:** Minimum 15-20 minutes for ASAP orders
3. **Clarify time format:** Validate "HH:MM" regex before parsing
4. **Document same-day only:** If no `pickup_date`, state clearly that orders are for today
5. **Better error messages:** "We're not open yet — try at 10:30" vs "Outside business hours"

## Summary

| Status | Count |
|--------|-------|
| ✅ PASS | 2 (time range, ASAP handling) |
| ❌ FAIL | 4 (day-of-week, lead time, date handling, holiday) |
| ⚠️ PARTIAL | 2 (invalid format, DST) |
| ❓ UNKNOWN | 2 (Monday hours, DST edge cases) |

**CRITICAL:** The NEW webhook is LESS SAFE than the legacy for hours validation because:
1. Legacy reads live `Open_Hours` sheet (can close for holidays/special days)
2. Legacy checks day-of-week; NEW does not
3. Legacy form enforces lead time; NEW does not

**Next:** Friday rotation — payment flow test (link generation → payment → deactivation)

---
*Generated by VALI on 2026-06-04 (Thursday). Reference workflows are learning material. NEW webhook must pass all checks before shipping.*
