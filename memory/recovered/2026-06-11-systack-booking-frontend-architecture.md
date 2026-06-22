# SyStack Booking Frontend + Test/Prod Architecture

**Date:** 2026-06-11 08:30 CDT  
**Source:** Oracle + Copal + Phillip  
**Status:** Planning complete, ready to build

---

## What's Needed

1. **Frontend booking form** on systack.net that matches DB schema 1:1
2. **Test/prod separation** — test page → test DB, prod page → prod DB
3. **Test database** `systack_test` with same schema as `systack_noshow`
4. **Frontend already submitted** — Phillip created:
   - `https://n8n.systack.net/webhook/booking-website-demo`
   - `https://n8n.systack.net/webhook/confirm-booking-website-demo`

---

## Architecture

### Production Path
```
systack.net/book
→ POST /webhook/systack-create-booking
→ Production n8n
→ systack_noshow.bookings
→ Production confirm link
```

### Test Path
```
systack.net/test-book
→ POST /webhook/booking-website-demo
→ Test n8n workflow
→ systack_test.bookings
→ Test confirm link
```

---

## Database Plan

| Database | Purpose | Status |
|----------|---------|--------|
| `systack_noshow` | Production bookings | ✅ Exists |
| `systack_test` | Test bookings | 📋 Need to create |

### Test DB Schema
Same as production:
```sql
CREATE TABLE bookings (
 id SERIAL PRIMARY KEY,
 customer_name TEXT NOT NULL,
 email TEXT NOT NULL,
 phone TEXT,
 service TEXT NOT NULL,
 appointment_time TIMESTAMP NOT NULL,
 status TEXT DEFAULT 'booked',
 confirmed BOOLEAN DEFAULT FALSE,
 confirmation_token TEXT UNIQUE,
 reminder_24h_sent BOOLEAN DEFAULT FALSE,
 reminder_24h_sent_at TIMESTAMP,
 reminder_2h_sent BOOLEAN DEFAULT FALSE,
 reminder_2h_sent_at TIMESTAMP,
 reminder_last_error TEXT,
 source TEXT DEFAULT 'website',
 created_at TIMESTAMP DEFAULT NOW(),
 updated_at TIMESTAMP DEFAULT NOW()
);
```

---

## Frontend Form Fields

| Field | Required | Maps To |
|-------|----------|---------|
| Customer Name | Yes | `customer_name` |
| Email | Yes | `email` |
| Phone | No | `phone` |
| Service | Yes | `service` |
| Appointment Date | Yes | `appointment_time` (combined with time) |
| Appointment Time | Yes | `appointment_time` |

### Submit JSON
```json
{
 "customer_name": "Jane Doe",
 "email": "jane@example.com",
 "phone": "5015551234",
 "service": "Haircut",
 "appointment_time": "2026-06-12 14:00:00",
 "source": "website"
}
```

---

## Frontend Template Requirements

### Colors (SyStack branding)
- Primary: Navy/teal/cyan (match existing site)
- Use same palette as systack.net

### Pages
| Page | Route | Purpose |
|------|-------|---------|
| Production | `/book` | Real customer bookings |
| Test | `/test-book` | Internal testing only |

### Test Page Warning
```html
<div style="background: #ff6b6b; color: white; padding: 12px; text-align: center;">
  ⚠️ TEST MODE — BOOKINGS ARE NOT REAL
</div>
```

---

## Workflow Mapping

| Workflow | Webhook | DB | Status |
|----------|---------|-----|--------|
| Prod Create | `systack-create-booking` | `systack_noshow` | ✅ Exists |
| Prod Confirm | `systack-confirm-booking` | `systack_noshow` | ✅ Exists |
| Prod Reminder | Schedule trigger | `systack_noshow` | ✅ Exists |
| **Test Create** | `booking-website-demo` → **should be `systack-create-booking-test`** | `systack_test` | 📋 Created, needs rename |
| **Test Confirm** | `confirm-booking-website-demo` → **should be `systack-confirm-booking-test`** | `systack_test` | 📋 Created, needs rename |
| **Test Reminder** | Manual trigger | `systack_test` | 📋 Need to clone |

---

## Test Checklist

- [ ] Create `systack_test` database
- [ ] Create `bookings` table in test DB
- [ ] Verify test workflow uses test DB credential
- [ ] Build `/test-book` frontend page
- [ ] Submit test booking → verify row in `systack_test.bookings`
- [ ] Verify confirmation email sends
- [ ] Verify confirm link updates test DB
- [ ] Verify reminders work in test
- [ ] Build `/book` production page
- [ ] Activate production only after test passes

---

## Escalation Rules

| Issue | Action |
|-------|--------|
| Test data in prod DB | Stop workflow, delete rows, verify credentials |
| Prod webhook gets test traffic | Check frontend URLs |
| Email not sending | Check SMTP credential, retry manually |

---

**Saved:** 2026-06-11 08:30 CDT  
**Next Action:** Create `systack_test` database
