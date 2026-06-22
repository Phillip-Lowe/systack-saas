# 2026-06-06 — Final Pricing Decision: $199 Minimum

## What Changed

**Killed:**
- SAOS Personal Basic ($49) — 4GB doesn't work
- SAOS Personal Pro ($99) — 8GB is tight, no margin

**Kept:**
- SAOS Personal+ ($199) — 16GB, works properly
- SAOS Business Fleet ($299) — team features
- SAOS Enterprise Fleet ($799) — on-premise

## Why $199 Minimum

| RAM | Experience | Cost | Verdict |
|-----|-----------|------|---------|
| 4GB | Broken, swapping, timeouts | $15/mo | ❌ Unusable |
| 8GB | Works but tight, slow | $48/mo | ⚠️ Frustrating |
| 16GB | Comfortable, responsive | $96/mo | ✅ Good |

**Jacqueline proved 4GB fails.** We won't sell failure.

## Cost Breakdown (Personal+ $199)

| Item | Cost |
|------|------|
| 16GB VPS (DigitalOcean) | $96/mo |
| Email support | $20/mo |
| **Total cost** | **$116/mo** |
| **Price** | **$199/mo** |
| **Margin** | **$83/mo** |

## Cloud LLM Policy

- Local models (Ollama) included in price
- Cloud LLM (Claude, ChatGPT) is OPTIONAL add-on
- User pays provider directly — no markup from us
- We help configure but don't bill for API usage

## What We Tell Prospects

> "We don't sell anything below $199 because anything less doesn't work. We learned with a 4GB deployment — it was unusable. Our minimum is 16GB RAM. That's what makes agents responsive and reliable."

## Percy's Place

**Percy = Personal+ ($199/mo)**
- 16GB VPS
- qwen2.5:7b
- Local dashboard
- Multi-device
- This is what we demo

## Files Updated

- `systack-site/services/service-packages.md` — Removed Basic/Pro, $199 minimum
- `saos-products/STRIPE-CATALOG.md` — 9 products, $199 minimum
- `saos-products/FINAL-PRICING.md` — Full rationale
- This memory file

## Stripe Products to Create

### High Priority
1. SAOS Personal+ Monthly ($199) — NEW
2. SAOS Personal+ Annual ($1,999) — NEW

### Medium Priority  
3. Systack Accelerate 10K Monthly ($249) — NEW
4. Systack Accelerate Setup ($2,500) — NEW
5. Systack Private Setup ($4,500) — NEW

### Keep Existing (Rename)
6. SAOS Business Fleet ($299) — rename from SAOS Business
7. SAOS Enterprise Fleet ($799) — rename from SAOS Enterprise

### Deprecate
8. SAOS Solo ($149) — replace with Personal+ ($199)

---

*Decision made: 2026-06-06 19:25 CDT*
*Rationale: Jacqueline's 4GB failure + cost reality + margin requirements*
