# 2026-06-06 — Template Deployment + Site Update

## What Was Done

### 1. Imported 6 Workflows to n8n

**Accelerate Tier (Cloud-Integrated):**
| Template | Workflow ID |
|----------|-------------|
| Generic Booking System | `JiDcFmiKglOiQPNm` |
| Client Onboarding | `1hGGv5Le4GpFDiR7` |
| Invoice Email Pipeline | `qnsBnLIWQ1Sky68D` |

**Private Tier (Zero Cloud Apps):**
| Template | Workflow ID |
|----------|-------------|
| Generic Booking System | `yNOVQ58AkASNHTBG` |
| Client Onboarding | `kj8sQLWfoQ8gc0Na` |
| Invoice Email Pipeline | `Ny4kzzf1bN4NODGn` |

### 2. Template Architecture Decision

Every template now has **two variants**:
- **Private:** SMS notifications, local filesystem, on-premise Postgres
- **Accelerate:** Slack notifications, Google Drive, cloud VPS Postgres

Both use local AI (Ollama). The split is about convenience vs. absolute privacy.

### 3. Site Updated

Updated `systack-site/services/service-packages.md`:
- Added detailed automation tables with notifications + storage per tier
- Added "Zero Cloud Apps" section for Private
- Added "Cloud Apps Used" section for Accelerate
- Updated comparison table with specific features (SMS vs Slack, local vs Google Drive)
- Added FAQ about tier differences and why no cloud-AI option exists

### 4. Documentation Created

| File | Purpose |
|------|---------|
| `templates/TEMPLATE-ARCHITECTURE.md` | Architecture decision + node substitution map |
| `templates/CLIENT-ONBOARDING-GUIDE.md` | What clients provide, what Systack provides, pricing |
| `templates/STRIPE-CREDENTIAL.md` | Stripe API credential details |
| `templates/DEPLOYMENT-STATUS.md` | All workflow IDs + activation checklist |
| `templates/README.md` | Template index + import instructions |

### 5. Key Pricing Principle Locked

- **Systack provides:** Postgres, n8n, local AI (fixed retainer)
- **Client provides:** Stripe, Google, Slack accounts (we help create)
- **Client pays directly:** Transaction fees, API overages (variable costs)
- **White-glove option:** +$500 setup fee for account creation service

## Files Changed
- `templates/` — Complete restructure with private/ + accelerate/ dirs
- `systack-site/services/service-packages.md` — Updated tier details
- `memory/2026-06-06-template-deployment.md` — This log

## Next
- Activate workflows after testing
- Build local dashboard for Private tier notifications
- Continue P1: new service line templates
