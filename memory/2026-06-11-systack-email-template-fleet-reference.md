# SyStack Branded Booking Email Template — Fleet Reference

**Date:** 2026-06-11  
**Status:** ✅ IMPLEMENTED  
**Owner:** ORACLE → SOL  
**System:** SyStack Booking / No-Show Prevention

---

## What This Is

Standardized SyStack-branded appointment confirmation emails for all n8n SMTP nodes in the booking workflows. Ensures polished, professional, on-brand emails across the entire no-show prevention system.

---

## Applies To

| Email Type | Workflow | Status |
|------------|----------|--------|
| Create Booking Confirmation | Booking workflow | ✅ Live |
| T-24h Reminder | Reminder scheduler | ✅ Live |
| T-2h Urgent Reminder | Reminder scheduler | ✅ Live |
| Future Rebooking Emails | (template ready) | 📋 Draft |
| Future Payment/Deposit Emails | (template ready) | 📋 Draft |

---

## Key Technical Rules

1. **HTML field must use real HTML** — not escaped entities
2. **HTML field must start with `=`** — enables n8n expression evaluation
3. **Uses SyStack brand palette** — navy, teal, cyan gradients
4. **Variables:** `{{ $json.customer_name }}`, `{{ $json.service }}`, `{{ $json.appointment_time }}`, `{{ $json.confirm_link }}`

---

## Brand Palette

```
Navy: #001a2d        Navy Light: #002845
Teal: #007da9         Cyan: #00a1db
Cyan Bright: #00c5e0  White: #ffffff
Gray 50: #f8fafc      Gray 100: #f1f5f9
Gray 200: #e2e8f0     Gray 400: #94a3b8
Gray 600: #475569      Gray 800: #1e293b
Red: #ef4444          Red BG: #fff5f5
Green: #22c55e         Green BG: #f0fdf4
Purple: #8b5cf6        Purple BG: #f5f3ff
```

---

## Required JSON Fields

```json
{
  "customer_name": "Test Customer",
  "email": "customer@example.com",
  "service": "Haircut",
  "appointment_time": "2026-06-12 14:00:00",
  "confirm_link": "https://n8n.systack.net/webhook/confirm-booking-website-demo?token=***"
}
```

---

## Common Failure Points (Pre-Checked)

| Issue | Cause | Prevention |
|-------|-------|------------|
| Raw HTML in email | Escaped entities used | Always paste real HTML tags |
| Variables don't render | Missing `=` prefix | Start HTML field with `=` |
| Confirm button broken | Missing `confirm_link` field | Verify prior node outputs it |

---

## Email Variants Available

| Variant | Subject | Headline |
|---------|---------|----------|
| Confirmation | `=Confirm your appointment: {{ $json.service }}` | "Confirm Your Appointment" |
| T-24h Reminder | `=Reminder: confirm your appointment tomorrow` | "Confirm Your Appointment" |
| T-2h Urgent | `=Urgent: your appointment is coming up soon` | "Your Appointment Is Coming Up Soon" |

---

## Validation Checklist

- [ ] Email renders visually, not raw HTML
- [ ] SyStack branding visible (navy header, cyan gradient button)
- [ ] Customer name renders
- [ ] Service name renders
- [ ] Appointment time renders
- [ ] Confirm button opens correct URL
- [ ] Confirm click updates DB status
- [ ] Template works in test workflow
- [ ] Template works in production workflow

---

## Fleet Memory Note

> SyStack branded confirmation email template is working in n8n SMTP nodes. Use official SyStack palette. Use real HTML tags, not escaped entities. HTML field must start with `=` so n8n evaluates booking variables and confirm_link.

---

**Original handoff from:** ORACLE  
**Implementation completed by:** SOL  
**Saved to fleet memory:** 2026-06-11
