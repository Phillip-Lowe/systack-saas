# Oracle Spec — Reminder Scheduler (T-24h + T-2h)

**Date:** 2026-06-11 07:30 CDT  
**Source:** Oracle directive  
**Status:** Ready to implement

---

## Architecture

Three separate workflows:
1. **Workflow 1:** Create Booking + Send Initial Confirm Email ✅
2. **Workflow 2:** Confirm Booking Link Handler ✅
3. **Workflow 3:** Reminder Scheduler — 24h + 2h 📋 (this spec)

---

## Database Migration

```sql
ALTER TABLE bookings
ADD COLUMN IF NOT EXISTS reminder_24h_sent BOOLEAN DEFAULT FALSE,
ADD COLUMN IF NOT EXISTS reminder_24h_sent_at TIMESTAMP,
ADD COLUMN IF NOT EXISTS reminder_2h_sent BOOLEAN DEFAULT FALSE,
ADD COLUMN IF NOT EXISTS reminder_2h_sent_at TIMESTAMP,
ADD COLUMN IF NOT EXISTS reminder_last_error TEXT;
```

---

## 24h Reminder Logic

Every 5 minutes:
- Find bookings where:
  - `confirmed = FALSE`
  - `status` NOT IN ('canceled', 'cancelled', 'completed', 'no_show')
  - `reminder_24h_sent = FALSE`
  - `appointment_time` within 24 hours
  - `appointment_time` > 2 hours away
- Send reminder email
- Mark `reminder_24h_sent = TRUE`

## 2h Reminder Logic

Every 5 minutes:
- Find bookings where:
  - `confirmed = FALSE`
  - `status` NOT IN ('canceled', 'cancelled', 'completed', 'no_show')
  - `reminder_2h_sent = FALSE`
  - `appointment_time` within 2 hours
  - `appointment_time` > NOW()
- Send urgent reminder email
- Mark `reminder_2h_sent = TRUE`
- Set `status = 'at_risk'`

---

## Test SQL

```sql
-- 24h test
UPDATE bookings
SET
 appointment_time = NOW() + INTERVAL '23 hours 59 minutes',
 confirmed = FALSE,
 status = 'booked',
 reminder_24h_sent = FALSE,
 reminder_2h_sent = FALSE
WHERE id = YOUR_BOOKING_ID;

-- 2h test
UPDATE bookings
SET
 appointment_time = NOW() + INTERVAL '1 hour 59 minutes',
 confirmed = FALSE,
 status = 'booked',
 reminder_2h_sent = FALSE
WHERE id = YOUR_BOOKING_ID;
```

---

## Completion Criteria

- [ ] 24h scheduler finds due booking
- [ ] 24h email sends
- [ ] `reminder_24h_sent = true`
- [ ] 2h scheduler finds unconfirmed due booking
- [ ] 2h email sends
- [ ] `reminder_2h_sent = true`
- [ ] `status = at_risk`

---

**Saved:** 2026-06-11 07:30 CDT
