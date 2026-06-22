# GENI Design: Payment Link Display
## Date: 2026-06-03 (Wednesday)
## Task: Design how the Square payment URL is presented to the customer

---

## What the Reference Shows

### Current n8n Flow
- Webhook returns JSON: `{ success: true, order_id, payment_link, message }`
- Customer gets an email with the payment link
- The current `order-form.js` success handler just shows: "✅ Order placed successfully! We'll have it ready..."
- **The payment link is NOT shown in the browser** — customer must check email

### Current Form Branding (HTML)
- Color palette: Navy `#001a2d`, Teal `#007da9`, Cyan `#00a1db`, Cyan-bright `#00c5e0`
- Cards: white background, rounded 16px, subtle border
- Buttons: gradient from cyan to teal, white text, rounded 12px, shadow
- Typography: system font stack, clean, modern
- Icons: emoji-based (🥪, 🛒, 👤)

---

## The Problem

If the customer doesn't see the payment link immediately:
1. They may not check email right away
2. They may think the order is complete (it's not — payment pending)
3. Kitchen may get notification before payment = confusion

The payment link is the most critical next action. It must be:
- **Prominent** — impossible to miss
- **Trusted** — clearly from Utopia Deli, not a sketchy redirect
- **Actionable** — one tap to pay
- **Persistent** — survives page refresh or back-button

---

## Design: Payment Link Card

### Location
Replace the generic success message (`#success-msg`) with a full payment link card that appears in the form area after successful submission.

### Structure

```html
<div id="payment-card" class="card payment-card" style="display:none;">
  <div class="payment-status">
    <div class="status-icon">🔒</div>
    <h2>Order Received — Payment Required</h2>
    <p class="status-sub">Your order <strong>#<span id="confirm-order-id"></span></strong> is waiting for payment.</p>
  </div>

  <div class="payment-cta">
    <a id="payment-url" href="#" target="_blank" rel="noopener" class="payment-btn">
      <span class="btn-icon">💳</span>
      <span class="btn-text">Pay Securely with Square</span>
      <span class="btn-arrow">→</span>
    </a>
    <p class="payment-note">You'll be redirected to Square's secure checkout.</p>
  </div>

  <div class="order-summary-mini">
    <h3>Order Summary</h3>
    <div id="confirm-items"></div>
    <div class="confirm-totals">
      <div class="row"><span>Subtotal</span><span id="confirm-subtotal"></span></div>
      <div class="row"><span>Tax</span><span id="confirm-tax"></span></div>
      <div class="row total"><span>Total</span><span id="confirm-total"></span></div>
    </div>
  </div>

  <div class="payment-fallback">
    <p>Link not working? <a href="#" id="payment-link-text" target="_blank">Open in new tab</a></p>
    <p class="copy-link">
      <button id="copy-payment-link" class="link-btn">📋 Copy Link</button>
    </p>
  </div>
</div>
```

### Visual Design Spec

#### Payment Card Container
- Same `.card` class as existing (white, rounded 16px, border)
- Additional `.payment-card` class for animations
- **Entrance animation**: fade up + scale from 0.95 → 1, duration 400ms, ease-out
- Border accent: 2px solid `var(--cyan)` on left side (using `border-left: 4px solid var(--cyan)`)

#### Status Section
- Icon: 48px circle with `var(--green-bg)` background, `var(--green)` checkmark or lock
- Heading: "Order Received — Payment Required" in `var(--navy)`, 20px, weight 700
- Subtext: Gray, includes dynamic order ID

#### Payment Button (Primary CTA)
```css
.payment-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  width: 100%;
  padding: 18px 24px;
  background: linear-gradient(135deg, #0070e0, #0055aa); /* Square brand blues */
  color: white;
  font-size: 17px;
  font-weight: 700;
  border-radius: 12px;
  text-decoration: none;
  transition: transform 0.1s, box-shadow 0.15s;
  box-shadow: 0 4px 14px rgba(0, 85, 170, 0.35);
}
.payment-btn:hover {
  transform: translateY(-1px);
  box-shadow: 0 6px 20px rgba(0, 85, 170, 0.45);
}
```
- **Why Square colors?** Builds trust — customer sees it's the payment provider they know
- Button text: "Pay Securely with Square" — clarity + trust
- Arrow icon (`→`) on right to indicate external navigation

#### Order Summary Mini
- Compact version of cart: item names × qty, line totals
- Totals section matches existing `.cart-totals` style
- **Why show this?** Confirms what they're paying for before they click

#### Fallback Options
- Plain text link (for accessibility / email sharing)
- "Copy Link" button — copies URL to clipboard, shows "Copied!" feedback
- Both styled as subtle text links, not competing with primary CTA

### Responsive Behavior
- Mobile: Full width, stacked layout
- Button: 100% width, larger touch target (min 48px height)
- Order summary collapsible? No — keep visible, customer needs to verify

### CSS Variables Used (matches form)
- `--navy`, `--cyan`, `--gray-*`, `--green`, `--red` — all existing vars
- No hardcoded colors except Square brand gradient (intentional — trust signal)

### JavaScript Behavior

```javascript
// In handleSubmit success block:
const data = await res.json();
if (data.success && data.payment_link) {
  // Hide form, show payment card
  document.getElementById('order-form').style.display = 'none';
  const card = document.getElementById('payment-card');
  card.style.display = 'block';

  // Populate data
  document.getElementById('confirm-order-id').textContent = data.order_id;
  document.getElementById('payment-url').href = data.payment_link;
  document.getElementById('payment-link-text').href = data.payment_link;

  // Populate order summary from local state
  renderConfirmationSummary();

  // Copy link functionality
  document.getElementById('copy-payment-link').onclick = async () => {
    await navigator.clipboard.writeText(data.payment_link);
    const btn = document.getElementById('copy-payment-link');
    btn.textContent = '✅ Copied!';
    setTimeout(() => btn.textContent = '📋 Copy Link', 2000);
  };
}
```

---

## Accessibility Considerations

- Payment button has clear text (not just "Click Here")
- External link indicators (`rel="noopener"`, `target="_blank"`)
- Copy link button for screen readers / keyboard users
- Order summary readable without color dependency
- Focus states on all interactive elements

---

## Pitfall Discovered

**PITFALL #9: Don't Rely on Email Alone**
The current flow assumes the customer will check email. Many won't — especially if they're ordering on mobile in a hurry. The payment link must be visible immediately in the browser.

**PITFALL #10: Don't Hide the Order ID**
The order ID is generated but not shown to the customer in the current success message. Customers need this for reference when they call or ask about their order.

---

## Files to Modify

- `systack-site/niches/food/index.html` — add payment card markup
- `systack-site/niches/food/order-form.js` — wire success handler to populate and show card
- `systack-site/niches/food/styles.css` (or inline `<style>`) — add payment card styles

---

## Next Steps

- Thursday: Design error state visuals (what happens when Square link fails?)
- Friday: Design loading states (submitting order, generating payment link)
- Saturday: Mobile responsiveness pass
- Sunday: Full visual audit

---

*Designed by GENI | 10-min creative sprint*
