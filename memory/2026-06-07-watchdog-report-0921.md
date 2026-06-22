# ERROR-WATCHDOG Report — Sunday, June 7, 2026 9:21 AM CDT

**Cron Job:** `e40a2803-30cf-4852-a272-9e456f29cb1d` (ERROR-WATCHDOG)  
**Triggered:** 9:21 AM CDT (14:21 UTC)  
**Checking:** CODY build sessions from 11 PM (Jun 6) and 1 AM (Jun 7)

---

## 🔴 VERDICT: BOTH BUILDS FAILED — Cron Jobs Broken, No Agent Execution

This is the **second consecutive night** with **no successful CODY builds**. The root cause has shifted from "agent/model failures" to **"cron job delivery channel is broken"** — the cron jobs themselves are erroring out before they can even spawn CODY.

---

## ❌ 11 PM Build: BUILD-VOICE-SKILL-Phase1 — CRON JOB FAILED

| Attribute | Value |
|-----------|-------|
| **Job ID** | `3955e592-a175-4050-8ad6-7ee96bb060b4` |
| **Schedule** | 23:00 CDT daily |
| **Expected Run** | Jun 6, 23:00 CDT (04:00 UTC, Jun 7) |
| **Actual Run** | Jun 6, 23:02:57 CDT |
| **Status** | ❌ **CRON DELIVERY FAILED** |
| **Error** | `Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>` |
| **Consecutive Errors** | 8+ |
| **Backoff** | 3600s (1 hour) |

**Log Evidence (Jun 6, 23:02:57 CDT):**
```
cron: job run returned error status
jobId=3955e592-a175-4050-8ad6-7ee96bb060b4
jobName=BUILD-VOICE-SKILL-Phase1
error="Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>"
cron: applying error backoff — consecutiveErrors=8, backoffMs=3600000
```

**Result:** CODY agent was **never spawned**. No build occurred. No artifacts.

---

## ❌ 1 AM Build: BUILD-CUSTOM-SKILLS-1AM — CRON JOB FAILED

| Attribute | Value |
|-----------|-------|
| **Job ID** | `8cf77c91-5c37-44a8-b8c8-33a985f5d062` |
| **Schedule** | 01:00 CDT daily |
| **Expected Run** | Jun 7, 01:00 CDT (06:00 UTC) |
| **Actual Run** | Jun 7, 01:01:14 CDT |
| **Status** | ❌ **CRON DELIVERY FAILED** |
| **Error** | `Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>` |
| **Consecutive Errors** | 8 |
| **Backoff** | 3600s (1 hour) |

**Log Evidence (Jun 7, 01:01:14 CDT):**
```
cron: job run returned error status
jobId=8cf77c91-5c37-44a8-b8c8-33a985f5d062
jobName=BUILD-CUSTOM-SKILLS-1AM
error="Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>"
cron: applying error backoff — consecutiveErrors=8, backoffMs=3600000
```

**Result:** CODY agent was **never spawned**. No build occurred. No artifacts.

---

## 🔴 ROOT CAUSE: BlueBubbles Delivery Channel Broken

**Same error affecting ALL cron jobs:**
- `ERROR-WATCHDOG` (65+ consecutive errors)
- `BUILD-VOICE-SKILL-Phase1` (8 consecutive errors)
- `BUILD-CUSTOM-SKILLS-1AM` (8 consecutive errors)
- `MONITOR-BUILD-JOBS` (13 consecutive errors)

**Error Pattern:**
```
"Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>"
```

All cron jobs are configured with `delivery.channel = "bluebubbles"` but **lack a valid target** (`--to` parameter). The cron system attempts to deliver the job payload via BlueBubbles, fails immediately, and never reaches the actual agent execution phase.

**This is a CONFIGURATION BUG, not a build failure.** The builds aren't failing — they're **not even starting**.

---

## 📊 Secondary Issue: Disk Space Crisis (Saturday Night)

While unrelated to the cron failure, the system experienced severe disk pressure on **Jun 6, ~22:05–22:30 CDT**:

- `ENOSPC: no space left on device` — session lock files, exec approvals, git operations all failing
- Model fallback chain triggered: `kimi-k2.6:cloud` → `deepseek-v4-pro:cloud` → `deepseek-v4-flash:cloud` → `qwen3.5:9b`
- Multiple SOL sessions crashed with disk-full errors
- This may have contributed to system instability but is **not the cause** of the cron job failures

---

## 🧾 CODY Agent Status

| Check | Result |
|-------|--------|
| CODY agent exists | ✅ Yes (`~/.openclaw/agents/cody/`) |
| CODY sessions directory | ✅ Has 105 session files |
| CODY skills directory | ⚠️ Empty (`~/.openclaw/workspaces/cody/skills/` has no entries) |
| Recent CODY sessions (12h) | ❌ None found via `sessions_list` |
| Session activity | ❌ No sessions from Jun 6 23:00 or Jun 7 01:00 |

---

## ✅ Previous Build Reference

**Last known successful builds:**
- **Jun 5, 23:00 CDT** — BUILD-VOICE-SKILL-Phase1 produced `PHASE1-REPORT.md` at 23:07
- **Jun 5, 01:00 CDT** — BUILD-CUSTOM-SKILLS produced 4 green skills at 01:01–01:04

No builds have succeeded since.

---

## 🎯 Action Required

**CRITICAL — Fix cron job delivery configuration:**

1. **Add `--to` target** to all cron jobs using BlueBubbles delivery, OR
2. **Switch delivery channel** from `bluebubbles` to a working channel (e.g., `webchat` with proper chatId), OR
3. **Use `mode: announce`** without a target if the job is meant to run locally without messaging

**Jobs needing fix:**
- `e40a2803-30cf-4852-a272-9e456f29cb1d` — ERROR-WATCHDOG
- `3955e592-a175-4050-8ad6-7ee96bb060b4` — BUILD-VOICE-SKILL-Phase1
- `8cf77c91-5c37-44a8-b8c8-33a985f5d062` — BUILD-CUSTOM-SKILLS-1AM
- `75998c8b-52af-4e92-9955-4f606aa95d0f` — MONITOR-BUILD-JOBS

**Current state:** All 4 cron jobs are in error backoff (3600s), meaning they'll retry every hour but fail the same way until the config is fixed.

---

*Report generated by ERROR-WATCHDOG cron job run (manual inspection via SOL agent).*
