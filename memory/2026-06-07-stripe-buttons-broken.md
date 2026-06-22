# Session Context — 2026-06-07 01:01 CDT

## Current Issue: Stripe Buy Buttons Show "Something went wrong"

Screenshot shows all 3 monthly buttons (Personal+ $199, Business $299, Enterprise $799) display "Something went wrong" instead of the checkout button.

The annual text links ("Save $389/year", "Pay annually") appear to work.

## What We Know

1. **Payment links are ACTIVE** in Stripe dashboard
2. **Links return 200** when tested via curl
3. **Buy Button IDs match** payment link IDs (format: buy_btn_[plink_id_without_prefix])
4. **Publishable key is LIVE mode** (pk_live_51Tckdx...)
5. **But embedded buttons show error** in browser

## Root Cause Analysis

Most likely causes:
1. **Payment link domain restrictions** — systack.net might not be in the allowlist
2. **Buy button feature not enabled** — need to enable "Buy Button" in Stripe dashboard for each payment link
3. **Account verification** — Stripe account might need additional verification
4. **Button ID format changed** — Stripe may have updated their buy button ID format

## What Works
- ✅ Annual text links (https://buy.stripe.com/...)
- ❌ Embedded Buy Buttons (buy-button-id)

## Temporary Solution
Replace embedded Buy Buttons with direct Stripe Checkout links:

Instead of:
```html
<script async src="https://js.stripe.com/v3/buy-button.js"></script>
<stripe-buy-button buy-button-id="..." publishable-key="...">
</stripe-buy-button>
```

Use:
```html
<a href="https://buy.stripe.com/7sYcMYfZLagn9wQ7MG87K03" class="cta-btn">Subscribe Monthly</a>
<a href="https://buy.stripe.com/bJecMYdRD88f8sM5Ey87K04" class="cta-btn">Subscribe Annual</a>
```

## Files Updated This Session
- systack-site/pricing.html — added annual options (but buttons broken)
- systack-site/personal-agent/index.html — added annual options (but buttons broken)
- systack-site/services/service-packages.md — fixed deprecated button IDs
- saos-products/STRIPE-CREDENTIAL.md — key documentation

## Stripe Product Catalog
All products created and active (see stripe-products.md for full list)

## Commit
TBD — need to fix buttons first

## Priority
P0 — customers can't sign up if buttons don't work

## Next Steps
1. Switch from Buy Buttons to direct checkout links
2. Test in production
3. Debug Stripe buy button issue separately
4. Document fix in MEMORY.md
