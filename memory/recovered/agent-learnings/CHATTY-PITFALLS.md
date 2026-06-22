# CHATTY Pitfalls — Interface / Communicate

## 1. Do Not Copy Legacy Email Templates Verbatim
The legacy `Order Received` email template was built for a multi-stage cart flow with Google Sheets state and Square payment link injection. The NEW webhook is a single POST — all data arrives complete. Copying the template structure would create unnecessary placeholders and fragile regex replacement logic.

## 2. Avoid Placeholder Injection for Static Content
Legacy template uses `__CART_HTML__`, `__PAYMENT_LINK__`, `__CURRENT_YEAR__` tokens injected via JavaScript. In the NEW webhook, the email body should be composed directly in n8n (or rendered server-side) using the complete JSON payload. No need for token-replacement code nodes.

## 3. Do Not Hardcode CSS in Email Body
Legacy template includes ~300 lines of CSS for light/dark themes. Most email clients strip or ignore complex CSS. Use simple inline styles for the NEW webhook notifications.

## 4. Match the Brand Voice, Not Just the Brand Colors
The legacy email has plum (`#590B3F`) headings, rose (`#AF3D4B`) CTAs, and gold accents. The NEW webhook should preserve the same voice (warm, confident, "It's just good food") but doesn't need the full CSS token system.

## 5. Customer Email ≠ Kitchen Email
The legacy workflow only sends a payment-link email to the customer. The NEW webhook (per ASSEMBLY's build) sends BOTH customer and kitchen emails. Kitchen notifications need item-level detail, modifiers, and pickup timing.

## 6. Keep Subject Lines Under 60 Characters
Gmail truncates at ~60 chars. Legacy subject "Your Utopia Deli secure checkout link" is 44 chars — fine. But "Your Utopia Deli order confirmation and payment instructions" would be cut off.

## 7. Don't Rely on `cart_html` Builder for NEW Webhook
The legacy `cart_html BUILDER` Code node converts `cart_items` with modifiers, take-offs, and add-ons into an HTML table. The NEW webhook payload already has `order_items` with `name`, `qty`, `price`, and `modifiers` array. Compose HTML directly or use a simpler template.

## 8. Include Order Summary, Not Just a Link
The legacy email is essentially "click here to pay." The NEW customer notification should show the actual order items so the customer can verify what they ordered before paying.

## 9. Include Payment Link Expiration Context
The legacy email doesn't mention the 2 AM expiration. The NEW webhook should (subtly) mention that links expire to create urgency.

## 10. Pickup Time Must Be Prominent
Both customer and kitchen emails need clear, scannable pickup time — this is the most important logistical detail.

## 11. Don't Use the Same Error Message for All Failures
The current v1.0.1 webhook returns a single `message` string for every error type. A "missing field" error and a "Square API down" error should have completely different tones — one is the customer's fault (friendly guidance), one is the system's fault (apology + contact). Match tone to error category.

## 12. Always Provide a Contact Fallback
If the online order system fails, the customer MUST have a way to complete their order. The legacy workflow and current v1.0.1 both leave customers stranded with no phone number or alternative. Every error response should include: "Call us at (501) 551-5944 to place your order by phone."

## 13. Kitchen Needs Error Visibility
The legacy workflow only notifies staff via Slack (Error Catcher). The NEW webhook doesn't notify anyone on errors. Orders that fail after submission are invisible to the kitchen — the customer might call, but the kitchen has no context. Add a kitchen error alert email with full payload for debugging.

## 14. Structure Error Responses for UI Parsing
The current v1.0.1 returns `{ success: false, message: "..." }`. The form UI needs to distinguish between field-level errors (highlight inputs) and system errors (show banner). Use a structured error object with `code`, `message`, `details[]`, `action`, and `contact` fields.

## 15. Payment Reminder Must Include Expiration Context (CHATTY-015)
If customers don't know the payment link expires at 2 AM CT, they procrastinate. When the link dies, they blame the business or give up. Every payment email (initial + reminder) must include: "This link expires at 2:00 AM CT." The reminder should be sent ~4 hours before expiration (e.g., 10 PM for 2 AM deadline).

## 16. No Refund Notification = Customer Confusion + Kitchen Waste (CHATTY-016)
When Square refunds an order, neither the legacy workflow nor the NEW webhook notifies anyone. The customer wonders "where's my money?" and may call angrily. The kitchen may prepare food that's already refunded. Always send a refund confirmation email to the customer (with timeline + re-engagement) and an alert to the kitchen ("DO NOT PREPARE"). Include both partial and full refund templates.
