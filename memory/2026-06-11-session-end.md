# Session End — 2026-06-11 05:15 CDT

## What We Did

### 1. Frontend Source Field Rule (REQUIRED)
**Rule:** Every frontend MUST send `source` field identifying itself in webhook payloads.

| Frontend | Source Value | File |
|----------|-------------|------|
| Pickup Order | `pickup-order` | `order-form.js` |
| Meal Prep | `meal-prep` | `catering-form.js` |

**Database:** `source TEXT DEFAULT 'meal-prep'`

**Files saved:**
- `memory/2026-06-11-frontend-source-field-rule.md` — full rule documentation

## Decisions Made
- Database default: `'meal-prep'` (matches n8n Code node hardcode)
- Both frontends already send `source` — no code changes needed

## Status
- ✅ pickup-order frontend sends `source: 'pickup-order'`
- ✅ meal-prep frontend sends `source: 'meal-prep'`
- ✅ DB schema updated with `source` column
- ✅ Rule documented in memory

## Wiki Updates
- Rule documented for future agents
- Placeholder for new frontend onboarding guide
