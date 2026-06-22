## 2026-06-05 — CHATTY: Payment Reminder Copy Design

### Task
Friday rotation: Design payment reminder copy for when Square payment link expires at 2 AM CT.

### What I Studied
1. **Legacy "Order Received" email** — rich HTML with branded template, logo, slogan, cart HTML table, CTA button. Subject: "Your Utopia Deli secure checkout link" (44 chars). NO expiration mentioned anywhere.
2. **NEW webhook v1.0.2 emails** — plain text only. Customer gets: "Your order (UDO-...) total is $XX.XX. Pay securely here: [link]. We'll start cooking as soon as payment clears." Kitchen gets item list. NO expiration context in either.
3. **Architecture diagram** — shows "2 AM Expiration" branch after Square Payment Link. Links are deleted at 2 AM if unused. Currently no reminder or expiration handling.

### Key Finding
**Both legacy and NEW webhook completely omit expiration context.** Customers don't know the link expires. When it dies at 2 AM, they blame the business or give up. This is a silent revenue leak.

### Payment Reminder Email Designed

**Subject:** "Your Utopia Deli order — pay by 2 AM" (38 chars)

**Body (HTML template for n8n):**
```html
<div style="font-family:Arial,sans-serif;max-width:600px;margin:auto;padding:24px;background:#FBF6F6;">
  <p style="color:#590B3F;font-size:22px;font-weight:700;margin:0 0 16px;">Your order is waiting</p>
  <p style="color:#1F1B1D;font-size:14px;margin:0 0 20px;">
    Hey {{customer_name}},<br><br>
    Your order ({{order_id}}) is ready — we just need your payment to start cooking.
  </p>
  <div style="background:#fff;border:1px solid #E6E1E1;border-radius:8px;padding:16px;margin-bottom:20px;">
    <p style="margin:0 0 8px;"><strong>Total:</strong> ${{total}}</p>
    <p style="margin:0 0 8px;"><strong>Pickup:</strong> {{pickup_time}}</p>
    <p style="margin:0;"><a href="{{payment_link}}" style="background:#AF3D4B;color:#fff;padding:12px 20px;border-radius:24px;text-decoration:none;font-weight:bold;display:inline-block;">Pay Now</a></p>
  </div>
  <p style="color:#8A8585;font-size:13px;margin:0 0 12px;">
    ⏰ This link expires at <strong>2:00 AM CT tonight</strong>. After that, your order will be released.
  </p>
  <p style="color:#8A8585;font-size:13px;margin:0;">
    Need help? Call us at <a href="tel:5015515944" style="color:#590B3F;">(501) 551-5944</a>
  </p>
  <p style="margin-top:24px;font-style:italic;color:#754681;font-size:14px;">The Utopia Deli. It's just good food.</p>
</div>
```

### Expired Order Follow-up (Morning After)

**Subject:** "Your Utopia Deli order expired — reorder?" (43 chars)

**Body:**
```
Hi {{customer_name}},

Your order ({{order_id}}) expired at 2:00 AM because we didn't receive payment. No worries — your card was never charged.

Want to reorder? Just visit:
https://www.theutopiadeli.com/order

Or call us: (501) 551-5944

— The Utopia Deli
```

### Implementation Notes

**Reminder trigger:** n8n Schedule trigger at ~10:00 PM CT daily → query Google Sheets for orders with `status = "pending_payment"` and `submitted_at < 22:00` → send reminder.

**Expiration trigger:** n8n Schedule trigger at 2:00 AM CT → query for unpaid orders → delete Square link + update sheet status to "expired" → send follow-up email (optional).

### Comparison

| Aspect | Legacy | v1.0.2 | NEW Design |
|--------|--------|--------|------------|
| Expiration mentioned | ❌ No | ❌ No | ✅ Clear deadline |
| Urgency without panic | N/A | N/A | ⚠️ Friendly tone |
| Contact fallback | ❌ No | ❌ No | ✅ Phone number |
| Order summary in reminder | ❌ No | ❌ No | ✅ Total + pickup |
| Brand slogan | ✅ Yes | ❌ No | ✅ Preserved |
| Re-engagement after expiry | ❌ No | ❌ No | ✅ Reorder prompt |

### New Pitfall Added
**CHATTY-015:** Payment reminder without expiration context loses orders. If customers don't know the link expires at 2 AM CT, they procrastinate. When the link dies, they blame the business. Every payment email must include: "This link expires at 2:00 AM CT."

### Files Updated
- `memory/agent-learnings/CHATTY-PITFALLS.md` — added CHATTY-015
- `memory/2026-06-05.md` — this entry

### Next
Saturday: Design refund confirmation copy