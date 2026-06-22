# VALI Pitfalls — Known Failure Modes (Validate / Review)

## Rule: Never repeat a known pitfall. If you find a new one, add it here.

---

## VALI-001: Validation Checklist Missing "Negative Test Cases"
- **Context:** When designing validation for the NEW webhook, it's easy to write tests that confirm "valid payloads pass." But the real test is "invalid payloads FAIL correctly."
- **Failure Mode:** A webhook that passes all positive tests but silently accepts malformed data (e.g., negative quantities, XSS strings, future-dated orders years ahead) is worse than no validation — it gives false confidence.
- **Fix:** Every validation rule MUST have both a PASS test (valid data succeeds) and a FAIL test (invalid data returns expected error with correct HTTP status and message).

## VALI-002: Test Payloads Drift from Production Data
- **Context:** Test payloads are often hand-crafted during development. As the HTML form evolves (new modifiers, new fields, new menu items), test payloads become stale.
- **Failure Mode:** A test passes with a handcrafted payload but fails in production because the actual client sends a slightly different structure (e.g., `modifiers` is `null` instead of `[]`, or `special_instructions` is omitted instead of `""`).
- **Fix:** Generate test payloads FROM the actual client code (`order-form.js`). Use the same data pipeline that produces real orders. Or: capture real production payloads (sanitized) and replay them as regression tests.

## VALI-003: "All-At-Once" Validation Can Leak Partial State
- **Context:** The NEW webhook validates everything in one POST before any external API calls. But if validation is split across multiple Code nodes (Validate JSON → Hours Gate → Verify Totals), a failure in node 3 means nodes 1 and 2 already executed.
- **Failure Mode:** In n8n, each Code node is a separate execution step. If node 3 throws, the workflow halts — but any side effects from nodes 1-2 (e.g., logging, temporary state) may persist. This is especially problematic if an early node writes to a database.
- **Fix:** Keep validation PURE (no side effects) until ALL checks pass. Use a single Code node for all validation logic, OR use a "validation gate" pattern where early nodes only validate and never write/log/mutate state.

## VALI-004: Test Coverage Gaps Between Per-Stage and All-At-Once
- **Context:** The legacy workflow has validation spread across multiple stages (Contact form, Item form, Cart form, Checkout form). Each stage validates its own slice. The NEW webhook must validate ALL slices in one shot.
- **Failure Mode:** It's easy to miss a validation rule that was implicit in the per-stage flow. For example, the legacy "Entree" form only shows certain options based on previous choices — the NEW webhook receives a flat JSON and must re-validate that the combination is legal.
- **Fix:** Create a "cross-walk" matrix: list every validation the legacy does at ANY stage, then mark whether the NEW webhook replicates it. Any gap = a test case.

## VALI-005: Environment Differences Hide Bugs
- **Context:** The NEW webhook will run on a different n8n instance (`utopia-api.systack.net`) than the legacy workflows (local n8n or different server). Node version, timezone settings, environment variables, and credential IDs may differ.
- **Failure Mode:** A test passes locally but fails in production because `$env.TAX_RATE` is unset, or `America/Chicago` timezone isn't configured on the server, or the Google Sheets credential ID doesn't exist.
- **Fix:** Tests must be environment-aware. Validate required env vars at startup. Run tests in a staging environment that mirrors production. Use a health-check endpoint that reports which env vars are set.

## VALI-006: `line_total_cents` Sent by Form but Never Validated by Webhook
- **Context:** The HTML form (`order-form.js`) sends `line_total_cents` for each item (`quantity * unit_price_cents`). The NEW webhook's `Validate JSON Structure` node checks `subtotal_cents` and individual item fields but NEVER verifies that `line_total_cents` matches `quantity * unit_price_cents`.
- **Failure Mode:** An attacker could set `line_total_cents: 0` on every item while keeping `subtotal_cents` manipulated to match the fake total. The webhook would pass all checks, Square would charge the customer the fake total, but the order summary would show $0 items — leading to customer disputes, chargebacks, or kitchen confusion.
- **Fix:** The webhook MUST independently calculate: `expected_line_total = item.quantity * item.unit_price_cents` for each item, and `expected_subtotal = Σ(expected_line_total)`. Reject if client-sent values diverge. Do NOT trust client-calculated totals.

## VALI-007: Hours Gate Assumes `pickup_time` Is Always Today
- **Context:** The webhook's Hours Gate treats "HH:MM" as today's time. There's no date component in the payload.
- **Failure Mode:** A customer sending "12:00" on a Monday might intend Tuesday pickup. The webhook checks Monday's hours, not Tuesday's. If Monday is closed but Tuesday is open, the order is wrongly rejected. Conversely, if Monday is open and Tuesday is closed, a customer expecting Tuesday pickup gets their order prepared on Monday.
- **Fix:** Either (a) add a `pickup_date` field to the form and webhook, (b) document that all orders are same-day only, or (c) reject orders submitted on closed days entirely with a clear message.

## VALI-008: No Lead Time = Kitchen Rush Orders
- **Context:** The legacy form enforces 20-minute lead time (can't select a time less than current time + 20 minutes). The NEW webhook has no lead time check — ASAP is literally current time.
- **Failure Mode:** Customer submits at 14:59 for ASAP pickup. Kitchen has 1 minute to prepare. Customer arrives at 15:00 to find kitchen closed. Or: submits at 10:30 for 10:30 pickup — impossible to prep in 0 minutes.
- **Fix:** Add minimum lead time to ASAP orders (e.g., 15-20 minutes). For specific times, verify `pickup_time >= current_time + lead_time`.

## VALI-009: No Payment Link Lifecycle Management
- **Context:** The NEW webhook generates a Square payment link but has NO workflows to handle what happens AFTER generation. The legacy architecture has explicit "Payment Completed" and "2 AM Expiration" workflows that manage link state (disable on payment, delete unused at 2 AM).
- **Failure Mode:**
  1. Customer pays successfully → link remains active in Square → could be reused or forwarded to others for payment
  2. Customer never pays → link stays active forever → clutter in Square dashboard, kitchen sees "pending" orders indefinitely
  3. No automated reconciliation → kitchen staff must manually check Square dashboard to see which orders were actually paid
  4. Square links have no built-in expiration by default — without explicit cleanup, they persist indefinitely
- **Fix:**
  1. Create "Payment Completed" webhook that listens for Square payment events → deactivates link → updates Sheets status to "paid"
  2. Create "Link Expiration" cron workflow (daily at 2 AM) → query Square for unpaid links created > 24h ago → delete → update Sheets status to "expired"
  3. Add `payment_link_status` column to Google Sheets (active, paid, expired, cancelled)
  4. Store `payment_link_created_at` timestamp for expiration logic
  5. Add link format validation after extraction (must start with `https://`, `payment_link_id` non-empty)

---

## VALI-010: End-to-End Tests Must Verify Every Handoff, Not Just the Webhook
- **Context:** It's tempting to test the webhook in isolation (post JSON, check response). But the real failure modes happen at handoffs: form→network, network→webhook, webhook→Square, webhook→email, webhook→Sheets.
- **Failure Mode:** Webhook tests pass in isolation, but production fails because the HTML form sends a slightly different field name, or CORS is blocked, or email SMTP is down, or Google API quota exceeded.
- **Fix:** End-to-end tests must trace the entire path: open form → fill fields → submit → verify webhook response → verify email delivery → verify Sheets row → verify Square link → (optional) verify payment completion.
- **Scope:** This is more than a unit test — it's an integration test requiring real infrastructure (or high-fidelity mocks).

---

## Meta: How VALI Works
1. Read the reference workflows (they are bug-free learning material).
2. Read what other agents found today (CODY, ASSEMBLY, CHATTY, GENI, PESSI).
3. Design ONE test or checklist for the NEW webhook.
4. Compare reference validation (per-stage) to NEW webhook validation (all-at-once).
5. Mark each item: PASS, FAIL, UNKNOWN.
6. If you discover a new pitfall → add to this file (VALI-NNN).
7. Append findings to `memory/shared-learning-dump.md` and `memory/YYYY-MM-DD.md`.
