# T-30min Auto-Release Workflow — DEPLOYED

**Date:** 2026-06-16 ~02:48 CDT  
**Workflow ID:** `KSmNNiADmPDOwJq0`  
**Status:** ✅ Active (every 5 minutes)  
**Builder:** SOL  
**Source:** `n8n-workflows/systack-auto-release-unconfirmed.json`

---

## What This Does

Runs every 5 minutes, queries `systack_noshow.bookings`, finds any unconfirmed appointments that are due within 30 minutes, and:

1. **Marks status = 'released'** — slot is free for rebooking
2. **Updates `released_at`** — audit trail
3. **Notifies customer via HTTP webhook** — their slot was released due to no confirmation

---

## Complete No-Show Prevention Priority Chain

| Stage | Trigger | Action | Status |
|-------|---------|--------|--------|
| **1** | Booking created | Store in DB, send confirmation email | ✅ Live |
| **2** | Customer clicks confirm link | `confirmed = TRUE`, send confirmation | ✅ Live |
| **3** | T-24h cron (daily) | Send reminder + "Confirm/Cancel" buttons | ✅ Active |
| **4** | T-2h cron (every 5 min) | Send urgent reminder + "I'm on my way" button | ✅ Active |
| **5** | **T-30min auto-release** (every 5 min) | **Check: confirmed? → YES: proceed / NO: release slot** | **🆕 JUST ACTIVATED** |

---

## Flow Diagram (Priority Chain)

```
┌─────────────┐     ┌──────────┐     ┌─────────────┐
│  Booking    │────▶│  Store   │────▶│ Confirmation│
│   Made      │     │   in DB  │     │   Email     │
└─────────────┘     └──────────┘     └──────┬──────┘
                                              │
                         ┌────────────────────┘
                         ▼
              ┌──────────────────────┐
              │ Customer confirms?    │
              │ (clicks link in email)│
              └──────────┬───────────┘
                         │
            ┌────────────┼────────────┐
            ▼            ▼            ▼
       ┌────────┐  ┌──────────┐  ┌──────────┐
       │  YES   │  │   NO     │  │  IGNORE  │
       │confirmed│  │(no action)│  │(reminders│
       │=TRUE   │  │          │  │fire)    │
       └──┬─────┘  └──────────┘  └────┬─────┘
          │                             │
          ▼                             ▼
    ┌────────────┐              ┌───────────────┐
    │  Proceed   │         T-24h│ Send reminder │
    │ normally   │              └───────┬───────┘
    └────────────┘                      │
                                 T-2h │ Send urgent  │
                                      │ reminder     │
                                      └──────┬──────┘
                                             │
                                        T-30min │ CHECK        │
                                               │ confirmed?   │
                                               └──────┬──────┘
                                                      │
                                         ┌────────────┼────────────┐
                                         ▼            ▼            ▼
                                    ┌────────┐  ┌──────────┐  ┌──────────┐
                                    │  YES   │  │   NO     │  │ MISSING  │
                                    │proceed │  │ RELEASE  │  │(safety  │
                                    │normally│  │ slot     │  │ check)   │
                                    └────────┘  └────┬─────┘  └──────────┘
                                                     │
                                                     ▼
                                              ┌──────────┐
                                              │ status=  │
                                              │'released'│
                                              │ notify   │
                                              │ customer │
                                              └──────────┘
```

---

## Database Changes

```sql
ALTER TABLE bookings
ADD COLUMN IF NOT EXISTS release_notified BOOLEAN DEFAULT FALSE,
ADD COLUMN IF NOT EXISTS released_at TIMESTAMP WITH TIME ZONE;
```

---

## Next Step in Priority Chain

This completes the No-Show Prevention system. Next build:

- **Smart Rebooking Engine** — Phase 1, highest impact
- **Review System** — Phase 1 completion

---

**Saved:** 2026-06-16 ~02:48 CDT