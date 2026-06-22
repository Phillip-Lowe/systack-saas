# SyStack Booking Frontend — Build Status

**Date:** 2026-06-11 08:56 CDT  
**Status:** Test page built, prod page queued

---

## Completed

### #5 — Prod DB Updated
- Added `phone TEXT` column to `systack_noshow.bookings`
- Added `source TEXT DEFAULT 'website'` column
- Prod DB now matches test DB schema (17 columns each)

### #2 — Test Page Built
- **File:** `systack-site/test-book.html`
- **URL:** Will be `systack.net/test-book`
- **Webhook:** `POST https://n8n.systack.net/webhook/booking-website-demo`
- **Features:**
  - SyStack branding (navy/teal/cyan)
  - TEST MODE banner (red)
  - Form fields: name, email, phone, service, date, time
  - Combines date+time into `appointment_time`
  - Submits JSON matching DB schema
  - Loading state + success/error messages

---

## Queued (Next)

### #4 — Production Page
- Clone `test-book.html` → `book.html`
- Remove TEST MODE banner
- Change webhook to `systack-create-booking`
- Change source to `website`
- Same styling

---

## Test Page Fields → DB Mapping

| Form Field | JSON Key | DB Column |
|------------|----------|-----------|
| Full Name | `customer_name` | `customer_name` |
| Email | `email` | `email` |
| Phone | `phone` | `phone` |
| Service | `service` | `service` |
| Date + Time | `appointment_time` | `appointment_time` |
| (hidden) | `source` | `source` |

---

## Files Created

| File | Purpose |
|------|---------|
| `systack-site/test-book.html` | Test booking form |
| `memory/2026-06-11-systack-booking-frontend-architecture.md` | Architecture spec |

---

**Next:** Build `/book` production page (clone of test, remove banner, change webhook)
