# NEW Kitchen Notification Design — Complete Order Webhook

**Agent:** CHATTY  
**Date:** 2026-06-03 (Wednesday)  
**Task:** Design NEW kitchen notification for complete-order flow  
**Reference:** Legacy "Order Received" workflow + NEW webhook v1.0.1

---

## What the Reference Says

The legacy workflow (Order Received) has **NO kitchen email at all**. It only:
1. Gets cart state from Google Sheets
2. Validates total
3. Builds cart HTML with modifiers, take-offs, add-ons
4. Creates Square payment link
5. Sends customer a rich HTML email with payment button
6. Updates Google Sheets row with payment_link_id

The NEW webhook v1.0.1 has a basic kitchen email:
- To: `kitchen@utopiadeli.com`
- Subject: `NEW ORDER: {{order_id}}`
- Plain text with:
  - Order ID, customer name, phone, email
  - Pickup time
  - Items list (qty × name @ $price)
  - Subtotal, Tax, Total
  - Special instructions
  - Payment link (for the kitchen to check status)

---

## What the NEW Webhook SHOULD Say

The NEW webhook receives the **complete order in one POST** — no multi-stage cart building. Kitchen needs **actionable detail at a glance**, not a payment-link-check tool.

### Design Goals
1. **Scannable on a phone** — kitchen staff check email on mobile
2. **Clear pickup time** — most important detail, top placement
3. **Item-level detail** — modifiers matter for prep (take-offs especially)
4. **No payment link** — kitchen doesn't need it; customer does
5. **Bold "NEW ORDER" header** — cut through notification noise
6. **Include customer contact** — for questions about the order

---

## NEW Kitchen Email — Complete Order

### Subject Line (under 60 chars)
```
NEW ORDER #{{order_id}} — Pickup {{pickup_time}}
```
Example: `NEW ORDER #UDO-20260603-472 — Pickup 12:30`

### Body (plain text, mobile-friendly)

```
═══════════════════════════════════════
  NEW ORDER — {{order_id}}
═══════════════════════════════════════

PICKUP: {{pickup_time}}

CUSTOMER
────────
Name:  {{customer_name}}
Phone: {{phone}}
Email: {{email}}

ORDER
─────
{{#each order_items}}
{{qty}}x {{name}} @ ${{price}}
{{#if modifiers}}
  {{#each modifiers}}
    {{#if is_take_off}}NO {{/if}}{{mod_name}}
  {{/each}}
{{/if}}
{{/each}}

────────
Subtotal: ${{subtotal}}
Tax:      ${{tax}}
Total:    ${{total}}

SPECIAL INSTRUCTIONS
──────────────────
{{special_instructions || "None"}}

Reply to this email if you have questions.
```

---

## What Changed vs. Reference

| Aspect | Legacy (Order Received) | NEW Webhook Kitchen Email |
|--------|----------------------|---------------------------|
| **Exists?** | ❌ No kitchen email | ✅ Yes, dedicated kitchen email |
| **Pickup time** | N/A (not in flow) | ✅ Prominent, top placement |
| **Item detail** | N/A | ✅ Full items + modifiers |
| **Payment link** | N/A | ❌ Removed — kitchen doesn't need it |
| **Customer contact** | N/A | ✅ Phone + email for questions |
| **Format** | N/A | Plain text, phone-scannable |
| **Special instructions** | N/A | ✅ Included |

---

## n8n Expression for NEW Webhook

Replace the existing `Email Kitchen` node text with:

```
═══════════════════════════════════════
  NEW ORDER — {{$json.order_id}}
═══════════════════════════════════════

PICKUP: {{$json.pickup_time}}

CUSTOMER
────────
Name:  {{$json.customer_name}}
Phone: {{$json.phone}}
Email: {{$json.email}}

ORDER
─────
{{$json.order_items.map(i => i.qty + 'x ' + i.name + ' @ $' + i.price.toFixed(2)).join('\n')}}

────────
Subtotal: ${{$json.subtotal.toFixed(2)}}
Tax:      ${{$json.tax.toFixed(2)}}
Total:    ${{$json.total.toFixed(2)}}

SPECIAL INSTRUCTIONS
──────────────────
{{$json.special_instructions || 'None'}}

Reply to this email if you have questions.
```

---

## Improvements Over v1.0.1 Kitchen Email

1. **Removed payment link** — kitchen doesn't need to check payment status; they just cook
2. **Visual separators** — `═` blocks make sections scannable on small screens
3. **Pickup time at top** — the #1 most important detail for kitchen workflow
4. **"Reply to this email"** — actionable CTA if there are questions
5. **Consistent alignment** — prices right-aligned, labels left-aligned
6. **Shorter subject** — `NEW ORDER #XXX — Pickup HH:MM` fits in notification previews

---

## Open Questions

1. Should kitchen get a **separate SMS** for urgent ASAP orders?
2. Should the email include **prep time estimate** (e.g. "15 min prep")?
3. Should we add **order timestamp** so kitchen knows when it came in?

---

## Next Task (Tomorrow)
Study error messages — what does the customer see when things fail?
