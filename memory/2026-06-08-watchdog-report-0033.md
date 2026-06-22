# ERROR-WATCHDOG Report — Monday, June 8, 2026 12:33 AM CDT

**Cron Job:** `e40a2803-30cf-4852-a272-9e456f29cb1d` (ERROR-WATCHDOG)  
**Triggered:** 12:33 AM CDT (05:33 UTC)  
**Checking:** CODY build sessions from 11 PM (Jun 7) and 1 AM (Jun 8)

---

## 🔴 VERDICT: NO BUILDS DETECTED — Third Consecutive Night of Complete Failure

**This is now THREE consecutive nights (Jun 5→6, Jun 6→7, and Jun 7→8) with zero build activity from CODY.**

The build jobs are configured and firing, but they fail immediately with a BlueBubbles delivery error — no actual build work is ever attempted.

---

## ❌ 11 PM Build: BUILD-VOICE-SKILL-Phase1 — FAILED ON DELIVERY

| Attribute | Value |
|-----------|-------|
| **Job ID** | `3955e592-a175-4050-8ad6-7ee96bb060b4` |
| **Schedule** | `0 23 * * *` (11:00 PM CDT daily) |
| **Agent** | `sol` (was `cody`) |
| **Triggered** | Jun 7, 23:01 CDT (04:01 UTC Jun 8) ✅ |
| **Status** | ❌ **FAILED — Error on delivery, no build executed** |
| **Error** | `Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>` |
| **Consecutive Errors** | 9 (now in 1-hour backoff) |
| **Next Attempt** | 1780977600000 ms epoch (~Jun 8, 04:00 CDT) |

**Log Evidence:**
```
2026-06-07T23:01:02.484-05:00 — cron: job run returned error status
  jobId=3955e592-a175-4050-8ad6-7ee96bb060b4
  jobName=BUILD-VOICE-SKILL-Phase1
  error="Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>"
2026-06-07T23:01:02.485-05:00 — cron: applying error backoff
  consecutiveErrors=9, backoffMs=3600000
```

---

## ❌ 1 AM Build: BUILD-CUSTOM-SKILLS-1AM — FAILED ON DELIVERY

| Attribute | Value |
|-----------|-------|
| **Job ID** | `8cf77c91-5c37-44a8-b8c8-33a985f5d062` |
| **Schedule** | `0 1 * * *` (1:00 AM CDT daily) |
| **Agent** | `sol` (was `cody`) |
| **Triggered** | Jun 7, 01:00 CDT (06:00 UTC Jun 8) ✅ |
| **Status** | ❌ **FAILED — Error on delivery, no build executed** |
| **Error** | `Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>` |
| **Consecutive Errors** | Unknown (same error pattern) |

**Log Evidence:**
```
2026-06-07T23:36:31.663-05:00 — cron list shows:
  8cf77c91-5c37-44a8-b8c8-33a985f5d062 BUILD-CUSTOM-SKILLS-1AM
  cron 0 1 * * * @ America/Chicago
  status: error, isolated
  "announce -> last (last -> no route, will fail-closed: Deliver...)"
```

---

## 🔍 Root Cause Analysis

### The Builds Never Actually Start

The cron jobs **do fire** at their scheduled times (23:01 and 01:00), but they fail **immediately** during the "announce/delivery" phase before any agent session is ever created. The error is:

```
Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>
```

This means:
1. ✅ Cron scheduler works — jobs trigger on time
2. ✅ Job configuration exists — both jobs are enabled with correct schedules
3. ❌ **Delivery/announcement fails** — the job tries to send results via BlueBubbles but has no target recipient configured
4. ❌ **No session is ever spawned** — the failure happens before the agent run begins
5. ❌ **No build work is attempted** — no code is checked, no compilation happens

### Why "No Route" / "Fail-Closed"

The cron jobs have `announce -> last` configured, meaning they try to deliver results to the "last" known channel. But:
- The `last` channel has "no route" (no valid delivery target)
- The system "fails-closed" (aborts rather than delivering to an unknown destination)
- This is a **BlueBubbles configuration issue**, not a build failure

### Agent Assignment Anomaly

Both build jobs are now assigned to `agent: sol` instead of `agent: cody`. From memory, these were originally CODY build jobs. The agent reassignment may have happened during a past configuration change. However, this is **not the root cause** — even as SOL jobs, they should still execute build tasks if delivery worked.

### CODY Agent State

- **Last CODY session:** `dbcaadf2-ca20-4490-93da-0353b684aeff` on May 31, 21:56 CDT
- **No CODY sessions since:** June 1–8, 2026
- **CODY agent directory exists:** `~/.openclaw/agents/cody/` ✅
- **CODY session files:** 54 total, newest = May 31
- **CODY has been essentially dormant for 8+ days**

---

## 📊 Error Pattern Summary

| Night | 11 PM Build | 1 AM Build | CODY Sessions |
|-------|-------------|-----------|---------------|
| Jun 4→5 | ✅ Succeeded | ✅ Succeeded | Yes |
| Jun 5→6 | ❌ No session | ❌ No session | None |
| Jun 6→7 | ❌ No session | ❌ No session | None |
| Jun 7→8 | ❌ Delivery fail | ❌ Delivery fail | None |

---

## 🛠️ Required Fixes

### Immediate (Tonight)
1. **Fix BlueBubbles delivery target** for cron jobs `3955e592` and `8cf77c91`
   - Add `--to` parameter with a valid handle or chat_guid
   - Or change delivery method to not require BlueBubbles
   - Or configure a default delivery target for the agent

2. **Verify CODY agent health**
   - Check if CODY can still spawn sessions successfully
   - Test with a manual session spawn to `agent: cody`

### Short-Term
3. **Investigate why jobs are assigned to `sol` instead of `cody`**
   - Determine if this was intentional or a configuration drift
   - If builds should run as CODY, reassign the jobs

4. **Add build success verification**
   - The builds should write a success marker (file, log entry, or memory)
   - The watchdog should check for this marker, not just session existence

### Long-Term
5. **Consider decoupling build execution from delivery**
   - A delivery failure should not prevent the build from running
   - Builds should execute and log results independently
   - Delivery/notification should be a separate, non-blocking step

---

## 📎 Full Context

- **Gateway restart:** Jun 7, 20:20 CDT (gateway came up, 2 missed cron jobs deferred)
- **Current gateway version:** Node v25.9.0
- **Build job backoff:** 1 hour (next attempt ~04:00 CDT Jun 8)
- **ERROR-WATCHDOG backoff:** Also in error state (consecutiveErrors: 82+)
- **CODY agent dormant:** Since May 31 (8 days)
- **No build artifacts:** No new files in `/tmp/`, build dirs, or session outputs
- **No error logs from builds:** Because builds never started

---

**Report filed:** 2026-06-08 05:33 UTC  
**Next watchdog check:** Should verify if delivery fix resolves the issue
