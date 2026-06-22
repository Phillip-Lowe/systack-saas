# Utopia Deli Confirmation Email — Test Results

**Date:** 2026-06-12 12:05 CDT  
**Status:** DB Fixed ✅ | Workflows Ready ✅ | Needs n8n Import ⏳

---

## DB Fixes Applied

### 1. Added Missing Columns
```sql
ALTER TABLE orders ADD COLUMN email_sent INTEGER DEFAULT 0;
ALTER TABLE orders ADD COLUMN email_sent_at TEXT;
ALTER TABLE orders ADD COLUMN reference_id TEXT;
```

### 2. Populated reference_id
```sql
UPDATE orders SET reference_id = order_id;
```

### 3. Created Test Order
```sql
INSERT INTO orders (order_id, reference_id, customer_name, customer_email, source, 
  cart_json, subtotal_cents, tax_cents, total_cents, email_sent) 
VALUES ('UDO-20260612-TEST001', 'UDO-20260612-TEST001', 'Test Customer', 
  'test@example.com', 'pickup-order', 
  '[{"name": "Cowboy Chikn", "qty": 2, "price": 13.00}]', 2600, 247, 2847, 0);
```

### 4. Sample Orders (ready for testing)
| order_id | customer_name | customer_email | source | email_sent |
|----------|---------------|----------------|--------|------------|
| UDO-20260612-TEST001 | Test Customer | test@example.com | pickup-order | 0 |
| UTO-1781212968226-VZGK5 | Syxx Smith | syxxxxx6@gmail.com | meal-prep | 0 |
| UTO-1781104057332-WE1OK | pp | plowe95@yahoo.com | pickup-order | 0 |

---

## Test Results

### Test 1: Frontend Webhook (utopia-confirmation-email)
```bash
curl -X POST https://utopia-api.systack.net/webhook/utopia-confirmation-email \
  -H "Content-Type: application/json" \
  -d '{"order_id":"UDO-20260612-TEST001","source":"pickup-order"}'
```
**Result:** ❌ 404 — Workflow not imported/active

### Test 2: Square Webhook (utopia-square-webhook)
```bash
curl -X POST https://utopia-api.systack.net/webhook/utopia-square-webhook \
  -H "Content-Type: application/json" \
  -d '{"type":"payment.updated","data":{"object":{"payment":{"status":"COMPLETED","reference_id":"UDO-20260612-TEST001"}}}}'
```
**Result:** ❌ 404 — Workflow not imported/active

---

## Next Steps

### Requires Manual Action (n8n UI)
1. **Import workflow:** `utopia-deli-revamp/utopia-confirmation-email-v3.json`
2. **Activate workflow:** Toggle ON in top-right
3. **Verify credentials:**
   - SQLite: `utopia-orders` → point to `/Users/philliplowe/.openclaw/workspaces/sol/utopia-deli-revamp/orders.db`
   - SMTP: `Utopia GMAIL SMTP account`

### Then Re-run Tests
1. Test 1: Non-payment event → expect skip
2. Test 2: Payment not completed → expect skip
3. Test 3: Valid payment + order found → expect email sent
4. Test 4: Duplicate payment → expect deduplication

---

## Files Ready for Import

| File | Purpose |
|------|---------|
| `utopia-deli-revamp/utopia-confirmation-email-v3.json` | Main confirmation workflow |
| `utopia-deli-revamp/utopia-simple-checkout-v4.json` | Updated checkout (fixed redirects) |

---

## DB Schema (Current)

```sql
CREATE TABLE orders (
    order_id TEXT PRIMARY KEY,
    created_at TEXT DEFAULT CURRENT_TIMESTAMP,
    status TEXT DEFAULT 'pending',
    customer_name TEXT,
    customer_email TEXT,
    customer_phone TEXT,
    customer_city TEXT,
    customer_county TEXT,
    cart_json TEXT,
    subtotal_cents INTEGER,
    tax_cents INTEGER,
    total_cents INTEGER,
    square_payment_link TEXT,
    square_order_id TEXT,
    notes TEXT,
    paid_at TEXT,
    source TEXT DEFAULT 'meal-prep',
    email_sent INTEGER DEFAULT 0,
    email_sent_at TEXT,
    reference_id TEXT
);
```

---

**Status:** Ready for n8n import. DB is fixed, test data is loaded, workflows are built.
