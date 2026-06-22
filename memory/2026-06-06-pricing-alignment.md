# 2026-06-06 — Pricing Alignment: Systack + SAOS + Percy

## Decision: Percy Lives in SAOS Personal+

Percy is the reference implementation for SAOS Personal+ ($99/mo). It's what we demo, what we document, what we improve.

**Percy is NOT sold** — it's the open-source reference. But we sell **"Percy-powered Personal Agents"** as configured installs.

## Product Lines Finalized

### Systack Services (B2B, Done-For-You)

| Product | Price | Billing | What It Is |
|---------|-------|---------|-----------|
| Systack Private | $799/mo | Monthly | On-premise automation |
| Systack Private Annual | $699/mo | Yearly | 10% discount |
| Systack Private Setup | $4,500 | One-time | Hardware install |
| Systack Accelerate 10K | $249/mo | Monthly | Cloud, 10K runs |
| Systack Accelerate 10K Annual | $199/mo | Yearly | 20% discount |
| Systack Accelerate 25K | $349/mo | Monthly | Cloud, 25K runs |
| Systack Accelerate Setup | $2,500 | One-time | Remote setup |

### SAOS Personal (B2C, Self-Managed)

| Product | Price | Billing | What It Is |
|---------|-------|---------|-----------|
| SAOS Personal | $49/mo | Monthly | Life assistant |
| SAOS Personal Annual | $499/yr | Yearly | Save $89 |
| SAOS Personal+ | $99/mo | Monthly | Power user, multi-device |
| SAOS Personal+ Annual | $999/yr | Yearly | Save $189 |

### SAOS Business (Self-Managed Teams)

| Product | Price | Billing | What It Is |
|---------|-------|---------|-----------|
| SAOS Business Fleet | $299/mo | Monthly | Team automation |
| SAOS Enterprise Fleet | $799/mo | Monthly | On-premise option |

## Stripe Buttons Status

### ✅ Already Created
| Button ID | Product | Price |
|-----------|---------|-------|
| `buy_btn_1TfU451WicviTxiig1l8JYjR` | SAOS Solo (DEPRECATED) | $149 |
| `buy_btn_1TfU3M1WicviTxiilXTJNolL` | SAOS Business Fleet | $299 |
| `buy_btn_1TfU1m1WicviTxiikPepeQO4` | SAOS Enterprise Fleet | $799 |

### ❌ Need to Create (11 products)
1. Systack Private Monthly — $799
2. Systack Private Annual — $699/mo ($8,388/yr)
3. Systack Private Setup — $4,500
4. Systack Accelerate 10K Monthly — $249
5. Systack Accelerate 10K Annual — $199/mo ($2,388/yr)
6. Systack Accelerate 25K Monthly — $349
7. Systack Accelerate Setup — $2,500
8. SAOS Personal Monthly — $49
9. SAOS Personal Annual — $499
10. SAOS Personal+ Monthly — $99
11. SAOS Personal+ Annual — $999

## Percy's Role

**Percy = SAOS Personal+ reference**
- Lives at the $99 tier
- Multi-device, voice, local dashboard, n8n, Ollama
- What we show prospects
- What we document
- What we improve first

**The $49 tier** = Basic version without voice, single device, simpler dashboard

## Files Updated
- `systack-site/services/service-packages.md` — Added Personal tiers
- `saos-products/PRODUCT-LINE.md` — Full product architecture
- `saos-products/STRIPE-CATALOG.md` — Stripe SKU reference

## Next Actions
1. Create 11 new Stripe products with buy buttons
2. Update site with new buy button IDs
3. Deprecate SAOS Solo ($149) — redirect to Personal+ ($99)
4. Build Personal tier templates (simpler than Business)
5. Test Percy as Personal+ demo
