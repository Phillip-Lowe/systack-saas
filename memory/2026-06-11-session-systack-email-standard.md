# 2026-06-11 — SyStack Email Standard Established

**Time:** 10:32 CDT  
**Decision:** All SyStack emails must use branded template system  
**Scope:** Universal — every email automation going forward

---

## What Was Decided

From now on, **ALL** SyStack-branded emails (not just booking) must use the standardized template:
- Booking confirmations & reminders
- Invoice notifications
- System alerts (errors, completions)
- Marketing emails
- Client onboarding
- Any future email automation

## Why This Matters

Previously: Email styling was per-build, inconsistent.
Now: One standard, fleet-wide consistency. Every customer touchpoint looks professional.

## Rule (Added to MEMORY.md and TOOLS.md)

### Required Elements
| Element | Requirement |
|---------|-------------|
| Header | Navy (#001a2d) with SyStack wordmark |
| Body | Gray 50 (#f8fafc) background |
| CTA Button | Cyan gradient (00a1db → 00c5e0) |
| Footer | Navy with contact info |

### Technical Rules
1. HTML field starts with `=`
2. Real HTML only — no escaped entities
3. Official SyStack palette mandatory
4. Test before deploy

## Files Saved

| File | Location | Purpose |
|------|----------|---------|
| Fleet reference | `memory/2026-06-11-systack-email-template-fleet-reference.md` | Full template spec |
| Session log | `memory/2026-06-11-session-systack-email-standard.md` | This file |
| Curated memory | `MEMORY.md` | Permanent rule |
| Tools reference | `TOOLS.md` | Quick lookup for builders |

## Validation

When building ANY email node in n8n:
- [ ] Read `memory/2026-06-11-systack-email-template-fleet-reference.md`
- [ ] Copy template, customize headline/body
- [ ] Test send to verify rendering
- [ ] Mark complete

---

**Repeat back:** SyStack email template is now a universal standard. Every email gets branded.
