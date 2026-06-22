# Lead Capture & Discovery Automation — Implementation Status

**Date:** 2026-06-05
**Status:** Frontend complete, n8n workflow ready, deployment pending

---

## What Was Built Tonight

### 1. n8n Lead Capture Webhook
**File:** `systack-lead-capture-webhook.json`

**Flow:**
```
Webhook (POST systack-lead-capture)
  → Process Lead (validation + scoring)
    → Valid? (if/else)
      → YES: Save to Sheets → Notify Phillip → Auto-Reply → Success Response
      → NO: Error Response
```

**Features:**
- CORS headers for systack.net
- Input validation (name, email, message length)
- Lead scoring algorithm (budget + users + volume + data tier)
- Google Sheets append (creates dated sheets)
- Email to Phillip with score in subject
- Auto-reply to lead with next steps
- JSON success/error responses

**Endpoint:** `https://utopia-api.systack.net/webhook/systack-lead-capture`

---

### 2. Updated Contact Form (`contact.html`)
**Changes:**
- Replaced `mailto:` action with webhook POST
- Added JavaScript handler (fetch API)
- Form validation + status messages
- Success: "Check your email for confirmation"
- Error: Fallback to direct email

**Fields captured:** name, email, interest, business, message, budget

---

### 3. Discovery Questionnaire (`discovery.html`)
**New page:** `/discovery.html`

**8-step quiz:**
1. Data type (Public/Internal/Confidential/Restricted)
2. Local-only requirement
3. Compliance (HIPAA/SOX/GDPR/PCI/None)
4. Users (1 / 2-5 / 6-20 / 20+)
5. Conversation volume (Light/Moderate/Heavy)
6. Budget ($50-100 / $100-200 / $200-500 / Enterprise)
7. Timeline (ASAP / 1mo / 3mo / Exploring)
8. Contact info

**Auto-calculates recommended tier:**
- Bronze ($70/mo) — light use, public data
- Silver ($130/mo) — business data, 2-5 users ✅ recommended
- Gold ($220/mo) — heavy use, confidential
- Platinum/Custom — on-premise/air-gapped

**Sends to same webhook** with answers + recommendation

---

### 4. Updated Personal Agent Page (`/personal-agent/index.html`)
**Changes:**
- Replaced $99/mo single plan with Bronze/Silver/Gold tiers
- Added VPS specs (4GB/8GB/16GB, model info)
- Added trust badge: "Local Models Only — Your Data Never Leaves Our Servers"
- Security note section with Tailscale explanation
- All CTAs point to `/discovery` instead of `/contact`

---

### 5. Updated Pricing Page (`/pricing.html`)
**Changes:**
- Added Personal Agent section with Bronze/Silver/Gold
- Updated SAOS Fleet plans: Starter ($299), Pro ($599), Enterprise (custom)
- Added on-premise/air-gapped callout
- Risk killers updated: "Local models only" + "Discovery questionnaire before commitment"
- CTA: "Start Discovery Questionnaire" instead of email link

---

### 6. Updated Services Page (`/services.html`)
**Changes:**
- Added Security & Compliance section with 4 trust features
- Local models, Tailscale VPN, data tier assessment, on-premise option

---

## Deployment Checklist

| Item | Status | Next Action |
|------|--------|-------------|
| n8n workflow JSON | ✅ Ready | Import into n8n via UI |
| Google Sheets setup | ⏳ Needed | Create "systack-leads" spreadsheet |
| Email credentials | ⏳ Needed | Configure SMTP in n8n |
| Deploy to GitHub Pages | ⏳ Needed | `git add . && git commit && git push` |
| Test form submission | ⏳ Needed | Submit test lead after deploy |
| Verify auto-reply | ⏳ Needed | Check email inbox |

---

## Lead Flow (Post-Deploy)

```
Visitor lands on site
  → Browses services/pricing
  → Clicks "Get Started" → goes to /discovery
  → Fills 8-step questionnaire
  → Submits → n8n webhook
    → Saves to Google Sheets
    → Emails Phillip (with lead score)
    → Auto-replies to lead
  → Shows recommendation + "Book a Call" CTA

OR

Visitor goes to /contact
  → Fills quick form
  → Submits → same webhook
  → Same notifications
  → Shows "We'll reply within 24h"
```

---

## Files Changed/Created

| File | Action |
|------|--------|
| `systack-site/contact.html` | Updated form to use webhook |
| `systack-site/discovery.html` | ✅ New — 8-step questionnaire |
| `systack-site/personal-agent/index.html` | Updated pricing tiers |
| `systack-site/pricing.html` | Updated SAOS + Personal Agent sections |
| `systack-site/services.html` | Added security section |
| `systack-lead-capture-webhook.json` | ✅ New — n8n workflow |
| `clients/mcdonalds-gm/DEPLOYMENT-PLAYBOOK.md` | Added discovery step |

---

## Next Session

1. **Deploy n8n workflow** — Import JSON, configure credentials
2. **Test end-to-end** — Submit form, verify sheets + emails
3. **Push site to GitHub** — `git push origin main`
4. **Verify live** — Test on systack.net

---

*Built by Sol*
*Lead capture + discovery automation complete*
