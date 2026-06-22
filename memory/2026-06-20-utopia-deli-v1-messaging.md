# Session — 2026-06-20 02:55 CDT
## Utopia Deli V1 Messaging System — Partial Build

### What Was Done

#### 1. Site Updates (All Pushed to GitHub Pages)
| File | Change |
|------|--------|
| `pickup-order/index.html` | Added consent text under EMAIL field |
| `catering/index.html` | Added consent text under EMAIL field |
| `pickup-order/privacy.html` | New: SMS/email terms, opt-out, data protection |
| `privacy.html` | New: root copy for link consistency |
| `pickup-order/menu-data.js` | Juice: single option $5.00 10oz |
| `catering/catering-form.js` | Juice: desc updated to 10oz |
| `privacy.html` + `pickup-order/privacy.html` | Clickable logo, footer slogan "It's just good food.", footer links to homepage |

#### 2. Database
- **Script:** `scripts/deli_square_data_pg.py`
- **Pulls:** 5,000 customers from Square API
- **Stores:** Local Postgres `utopia_deli.contacts`
- **Export:** `utopia-contacts.csv` (356 after cleanup)
- **Cleanup:** Removed 5,179 no-contact, 71 fake names, deduped by square_id
- **Result:** 333 with email, 256 with phone, 233 with both

#### 3. Documentation
- **MESSAGING-RUNBOOK.md** — 5 email templates, 3 SMS templates, content pillars, weekly schedule

### Still To Do
| Task | Blocker |
|------|---------|
| Twilio signup | Phillip needs to do this |
| n8n messaging workflows | Waiting on Twilio creds |
| Opt-out handling (STOP parser) | Post-Twilio |
| List segmentation | Post-database setup |

### Git Commits
- `66e2377` — feat: opt-in consent text + privacy page
- `a1f7235` — feat: Square-to-Postgres sync script
- `eda5fba` — docs: messaging runbook
- `35ee03f` — fix: privacy page improvements + database cleanup
- `3d982ae` — fix: juice price + footer logo
- `1fa70a6` — fix: consent text under email, juice $5 10oz
- `9d96e84` — revert: juice back to original
- `ccde0e5` — fix: juice single option 10oz $5.00
- `22f94b8` — fix: meal prep juice desc updated to 10oz

### Notes
- Consent text placement moved from phone to email per user request
- Juice simplified to single $5.00 10oz option
- User confirmed they will revert juice pricing later when they bring back 16oz
