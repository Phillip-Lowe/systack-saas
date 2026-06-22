# 2026-06-08 — Complete Catering Lead System Deployment

## Overview
Complete catering/event lead capture + scoring + automated response + SQLite logging system for Utopia Deli.

## Payment Policy Update (Deli Partners)
- **50% deposit** when invoice is sent to book the event
- **Balance due** 2 weeks prior to the event
- **Events within 2 weeks:** Full payment upfront to book

This policy is reflected in:
1. Frontend form payment options and policy text
2. ACCEPT email template (step 3, 4, 5)
3. Success page "next steps" section

## What Was Built

### Frontend
- `catering/index.html` — 5-step form at https://order.theutopiadeli.com/catering/
- `pickup-order/index.html` — Main order page at https://order.theutopiadeli.com/pickup-order/
- `catering/catering-form.js` — validation + webhook POST
- URL changed from `/catering.html` to `/catering/` (clean URL)
- Redirect added from old URL to new

### Backend (n8n)
- Workflow ID: `T67LTu32k1xENtzd` — "Utopia Deli — Catering Lead Scoring"
- Webhook: `POST https://utopia-api.systack.net/webhook/utopia-deli-catering-v2`
- Status: ✅ ACTIVE (full scoring + email + SQLite)

### Database
- SQLite database: `~/.openclaw/workspaces/sol/utopia-deli-catering.db`
- Table: `catering_leads` with 35+ columns
- First record inserted: UDC-20260608-342

## Key Technical Discovery: API Key Shell Corruption Bug

**Problem:** When reading n8n API key via shell (`KEY=$(cat file)`), zsh corrupts JWT strings containing certain character sequences, causing `401 unauthorized` errors.

**Symptoms:**
- API key works fine when copied directly into curl command
- Same key fails when stored in shell variable
- Python file I/O reads the key correctly

**Solution:** Always use Python to read and pass API keys to curl, never shell variable expansion.

## Deployment History
1. v1 deployed (basic webhook)
2. v2 deployed (scoring + emails)
3. v2.1 (SQLite logging)
4. URL changed to /catering/
5. Payment terms updated per deli partners

## Files
- `CATERING-DEPLOYMENT-STATUS.md` — Full system documentation
- `CATERING-PLAN.md` — Architecture spec
- `utopia-deli-catering-v4.json` — n8n workflow spec
- `.catering_workflow_id` — Workflow ID reference
- `utopia-deli-catering.db` — SQLite database

## Next Steps
- Build owner dashboard for reviewing leads
- Implement true conditional routing (ACCEPT only → owner notify)
- Add SMS notification for high-value leads

**Session timestamp:** 2026-06-08 07:43 CDT
**Status:** Production ready, fully deployed


### Logo Path Fix (2026-06-08 07:58 CDT)
- Problem: Logo not showing after moving to `/pickup-order/` subdirectory
- Cause: `config-v2.js` had `logo: "images/logo.png"` (root-relative)
- Fix: Changed to `logo: "../images/logo.png"` in `pickup-order/config-v2.js`
- Also fixed favicon path
