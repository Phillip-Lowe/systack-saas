# GENI Pitfalls — Creative/Generate Agent

## Known Pitfalls

### 1. Don't Over-Design
The reference n8n workflows are FUNCTIONAL, not beautiful. Don't try to make everything pixel-perfect. The HTML form already has its own branding — my job is to match it, not reinvent it.

### 2. Don't Copy n8n Form Styling
n8n forms have their own default look (bootstrap-like). The HTML form at order.theutopiadeli.com has CUSTOM branding. Study the HTML form, not the n8n forms.

### 3. Mobile-First, Always
The deli customers will mostly order on their phones. Every design decision should prioritize mobile experience.

### 4. Don't Forget Error States
A beautiful success page means nothing if the error page looks broken. Design error states with the same care as success states.

### 5. Keep It Simple
One visual element per day. Don't try to design the entire confirmation flow in one session.

### 6. Don't Inline Critical UI
The current confirmation page is created via `innerHTML` in JavaScript with inline styles. This is fragile — if JS fails, the user sees nothing. Critical UI should be in the HTML with visibility toggling, not DOM injection.

### 7. Match the Form's CSS Variables, Not Hardcoded Colors
The form uses CSS custom properties (`--ud-primary`, `--ud-accent`, etc.). The confirmation page should use these same variables for consistency. Hardcoded hex codes will drift from the form's design system over time.

### 8. Show Order Details on Confirmation
A generic "We Got You!" message is not enough. The customer just submitted their order — they need to see WHAT they ordered, for HOW MUCH, and WHEN to pick it up. This builds trust and reduces anxiety.

### 9. Don't Rely on Email Alone for Critical Next Actions
The webhook returns a `payment_link`, but the current form only shows a generic success message and assumes the customer will check email. Many won't — especially on mobile. Critical next steps (like paying) must be visible immediately in the browser.

### 10. Don't Hide the Order ID
The order ID is generated server-side and returned in the webhook response, but the current success handler doesn't display it. Customers need this for reference when calling about their order or asking "where's my food?"

### 11. Generic Error Visuals Train Customers to Ignore Errors
When every error looks the same (red banner, disappears quickly), customers learn to dismiss them without reading. A validation error about a missing field and a system error about Square being down should look and feel DIFFERENT. One says "you can fix this," the other says "we're sorry, here's how to reach us."

**Fix:** Design category-specific error visuals with different colors, icons, titles, and CTAs. Make the visual match the emotional tone of the error. User errors = warm orange (actionable). System errors = burgundy (apologetic, on-brand). Business errors = teal (informative).

### 12. Static "Placing Order…" Button Text Is Insufficient
A disabled button with static text gives zero feedback during a 3-8 second webhook call. On mobile, the button may be off-screen. Users think the site is frozen, hit back, and submit duplicate orders.

**Fix:** Full-screen loading overlay with animated spinner, progress bar, and phase steps (Validating → Processing → Payment). Lock scroll so user can't interact. Show estimated time. Match brand colors (navy overlay, teal spinner).

### 13. Loading State Should Not Be DOM-Injected
If the loading UI is created via `innerHTML` in JS and the JS errors, the user sees nothing — just a frozen page. The loading overlay must exist in the HTML with `display:none` / class toggle.

**Fix:** Put the loading overlay HTML in the form's HTML file. Toggle visibility with CSS class, not DOM injection.

### 14. Progress Steps Reduce Perceived Wait Time
A single "Loading…" message feels longer than showing discrete steps. Users tolerate waits better when they see progress.

**Fix:** Show 3 phases: (1) Checking order, (2) Preparing order, (3) Creating payment link. Animate progress bar between phases. Update phase labels in real time.

### 15. Confirmation Page Must Be in HTML, Not Injected
The confirmation page (order success overlay) must exist in the HTML file with `display:none`, toggled via class. If it's injected via `innerHTML` in JS and the JS fails, the customer sees nothing after submitting — just a frozen form. This is worse than a loading state failure because the order may have already been placed.

**Fix:** Put the confirmation overlay HTML in `index.html`. Toggle visibility with CSS class, not DOM injection. The JS only populates data, not creates structure.

### 16. Mobile Confirmation Needs Top-Aligned Card, Not Centered
On desktop, a vertically-centered modal looks elegant. On mobile (especially iPhone with dynamic island / notch), a centered modal can be obscured by the notch, feel too low, or push the CTA below the thumb zone. Top-aligned with `margin-top: 8vh` keeps the header visible and the payment button in the natural thumb zone (bottom third of screen).

**Fix:** `align-items: flex-start` on mobile, `align-items: center` on desktop (`@media min-width: 640px`). Use `8vh` top margin on mobile, `0` on desktop.

### 17. Payment Button Must Be Full-Width and Thumb-Friendly on Mobile
A payment link displayed as raw text or a small inline button is easy to miss and hard to tap. On mobile, the primary CTA ("Pay Securely") must be:
- Full width of the card
- Minimum 48px tall (WCAG 2.5.5)
- Large touch target with visual affordance (shadow, gradient)
- Clearly labeled with icon for scanability

**Fix:** `.payment-btn { width: 100%; padding: 16px; font-size: 16px; }` with `:active { transform: scale(0.98); }` for tactile feedback.

### 18. Order ID Must Be Visible and Copyable
The order ID (`UDO-YYYYMMDD-###`) is critical for customer service, but on mobile it's often buried or omitted. It needs to be:
- Immediately visible (top of card)
- In a monospaced font for clarity
- Styled as a "badge" for visual hierarchy
- Not just in the email — many customers won't check email immediately

**Fix:** `.order-id { font-family: monospace; background: var(--gray-100); padding: 6px 12px; border-radius: 8px; }`

### 19. Reduced Motion Support Required
Some users have `prefers-reduced-motion: reduce` set. The card slide-up animation and button transitions should respect this. Failure to do so can cause accessibility issues (vestibular disorders) and feels unprofessional.

**Fix:** Wrap animations in `@media (prefers-reduced-motion: no-preference)` or provide `@media (prefers-reduced-motion: reduce)` overrides that disable transforms and animations.

### 20. Confirmation Overlay Must Lock Body Scroll
When the confirmation card appears, the underlying form page must not scroll. If it does, the user can accidentally scroll the form into view, try to interact with it, and get confused. Also, background content peeking through reduces focus on the confirmation.

**Fix:** On show: `document.body.style.overflow = 'hidden'`. On hide: restore to `''`. Also add `touch-action: none` if needed on the overlay itself.

## To Be Continued...
