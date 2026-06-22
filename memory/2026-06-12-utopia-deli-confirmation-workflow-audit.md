# Utopia Deli Confirmation Email Workflow — Audit Log

**Date:** 2026-06-12 12:01 CDT  
**Auditor:** SOL  
**Status:** IN PROGRESS — DB schema fixed, workflow verification needed

---

## 1. Database Audit

### DB Location
`/Users/philliplowe/.openclaw/workspaces/sol/utopia-deli-revamp/orders.db`

### Schema Check
**BEFORE (broken):**
```sql
-- email_sent and email_sent_at columns MISSING
```

**AFTER (fixed):**
```sql
ALTER TABLE orders ADD COLUMN email_sent INTEGER DEFAULT 0;
ALTER TABLE orders ADD COLUMN email_sent_at TEXT;
```

### Order Count
- **Total orders:** 26
- **email_sent=0:** All (new column)

### Sample Orders
| order_id | customer_name | customer_email | source | email_sent |
|----------|---------------|----------------|--------|------------|
| UTO-1781104057332-WE1OK | pp | plowe95@yahoo.com | pickup-order | 0 |
| UTO-1781103935217-5EEBH | pp | plowe95@yahoo.com | pickup-order | 0 |
| UTO-1780816837552-7Q2NI | PP | PLOWE95@yahoo.com | pickup-order | 0 |

**Observation:** Order IDs use `UTO-` prefix (not `UDO-` as documented). Source is `pickup-order` (not `online-order`).

---

## 2. Workflow File Audit

### Files Present
| File | Status | Notes |
|------|--------|-------|
| `utopia-confirmation-email-v3.json` | ✅ Built | Latest version with ORACLE fixes |
| `utopia-simple-checkout-v4.json` | ✅ Updated | Redirect URLs fixed |
| `payment-confirmed/index.html` | ✅ Deployed | Pickup success page |
| `payment-confirmed-meal-prep/index.html` | ✅ Deployed | Meal prep success page |

### Known Issues (from ORACLE handout)

#### Issue 1: Routing Structure
**ORACLE says:** Frontend and Square webhooks must NOT both route through same normalize

**Current:** `utopia-confirmation-email-v3.json` has separate `Normalize Frontend` and `Normalize Square` nodes → both route to single `Route Trigger` node

**Status:** ⚠️ Needs verification in n8n UI

#### Issue 2: Escaped Characters
**ORACLE says:** `&amp;&amp;`, `&lt;table&gt;`, `=&gt;` may be chat artifacts

**Current:** JSON files use escaped `&`, `<`, `>` (Unicode escapes in JSON)

**Status:** ✅ Expected in JSON — n8n imports will decode to `&&`, `<`, `>`

#### Issue 3: SQLite Module
**ORACLE says:** Code nodes may use `require('sqlite3')`

**Current:** Workflow uses native n8n `sqlite` node type, NOT Code nodes with require

**Status:** ✅ Native node is more reliable

#### Issue 4: Database Path
**Current path:** `/Users/philliplowe/.openclaw/workspaces/sol/utopia-deli-revamp/orders.db`

**Status:** ✅ File exists, 26 rows, writable

---

## 3. Order ID Prefix Mismatch

**Documented:** `UDO-` for pickup, `UMP-` for meal prep
**Actual DB:** `UTO-` for pickup, `UTP-` or `UMP-` for meal prep

**Impact:** `reference_id` in Square must match actual `order_id` in DB

**Recommendation:** Update checkout workflow to use `UTO-` prefix for pickup orders (matching DB convention)

---

## 4. Test Plan Status

| Test | Status | Notes |
|------|--------|-------|
| Test 1: Non-payment event | ⏳ Pending | Need to run in n8n |
| Test 2: Payment not completed | ⏳ Pending | Need to run in n8n |
| Test 3: Missing order ID | ⏳ Pending | Need to run in n8n |
| Test 4: Order not found | ⏳ Pending | Need to run in n8n |
| Test 5: Email already sent | ⏳ Pending | Need to run in n8n |
| Test 6: No customer email | ⏳ Pending | Need to run in n8n |
| Test 7: Successful confirmation | ⏳ Pending | Need to run in n8n |

---

## 5. Action Items

### Immediate (Green approval NOT needed)
- [ ] Verify n8n workflow wiring matches ORACLE handout
- [ ] Import `utopia-confirmation-email-v3.json` to n8n
- [ ] Test with controlled payload

### Requires Green Approval
- [ ] Update checkout workflow `reference_id` to match DB `order_id` format
- [ ] Activate webhook in Square Developer Dashboard
- [ ] Deploy to production server

---

## 6. Files for n8n Import

1. `utopia-deli-revamp/utopia-confirmation-email-v3.json` — Main confirmation workflow
2. `utopia-deli-revamp/utopia-simple-checkout-v4.json` — Updated checkout (redirect URLs)

---

**Next Step:** Import workflows to n8n and run Test 1 (non-payment event)
