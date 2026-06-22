# 2026-06-06 — Realistic Pricing: VPS Costs Exposed

## Problem Identified

User correctly pointed out: **$49/mo is not realistic** for a personal agent.

Why:
- VPS costs $15-24/mo minimum (4GB RAM)
- GPU VPS costs $60-80/mo
- LLM inference needs RAM/VRAM
- Support costs money
- We need to make margin

## Cost Analysis Done

**Minimum viable infrastructure:**
- 4GB RAM, 2 vCPU VPS = $15-24/mo
- 8GB RAM, 4 vCPU + GPU = $62-80/mo

**Shared vs Dedicated:**
- Shared (10 users/VPS): $6/user/mo cost
- Dedicated (1 user/VPS): $24/user/mo cost
- GPU dedicated: $80/user/mo cost

## New Pricing Structure

| Product | Price | Infrastructure | Target |
|---------|-------|---------------|--------|
| **SAOS Personal Basic** | $49/mo | Shared VPS, CPU | Students, budgets |
| **SAOS Personal Pro** | $99/mo | Dedicated 4GB VPS | Professionals |
| **SAOS Personal+** | $149/mo | Dedicated 8GB + GPU | Power users (Percy) |
| **SAOS Business Fleet** | $299/mo | Team features | Small teams |
| **SAOS Enterprise Fleet** | $799/mo | On-premise | Large orgs |

## Why This Works

**$49 Basic:** Shared VPS, 10 users per server. Our cost: ~$14/mo. Margin: $35/mo.

**$99 Pro:** Dedicated 4GB VPS. Our cost: ~$35/mo. Margin: $64/mo.

**$149 Personal+:** Dedicated 8GB + GPU. Our cost: ~$80/mo. Margin: $69/mo.

## Transparency

We tell users:
- **Basic:** "Runs on shared infrastructure. Secure isolation, but shared resources."
- **Pro:** "Your own dedicated server. Faster, more reliable."
- **Personal+:** "GPU-powered. Fastest inference, premium support."

## Percy's Place

**Percy = Personal+ ($149/mo)**
- This is what we demo
- This is what we document
- Full feature set, GPU speed

## Files Updated
- `saos-products/cost-analysis/REALISTIC-PRICING.md` — Full cost breakdown
- `systack-site/services/service-packages.md` — Updated Personal tiers with infra details

## Next
- Update Stripe products to match new pricing
- Create buy buttons for Basic ($49), Pro ($99), Personal+ ($149)
- Deprecate old SAOS Solo ($149) — Personal+ replaces it
