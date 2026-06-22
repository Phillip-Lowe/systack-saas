# ERROR-WATCHDOG Report — June 4, 2026 10:26 AM CDT

## Executive Summary

**Both CODY build jobs FAILED last night.** Same failure pattern as the previous 4 nights — LLM request timeouts on `kimi-k2.6:cloud`, then fallback to `qwen3.5:9b` also times out. **No build artifacts produced.** The ERROR-WATCHDOG itself is also broken — delivery misconfiguration prevents notifications from reaching Green.

---

## Build Job Results (June 3 → June 4)

### BUILD-VOICE-SKILL-Phase1 (11 PM CDT / 04:00 UTC)
- **Status:** ❌ FAILED
- **Session:** `e23dfd50-e731-439a-9bbe-ba077b1e7971`
- **Started:** 2026-06-04 04:00 UTC (Jun 3 11:00 PM CDT)
- **Ended:** 2026-06-04 04:09 UTC (Jun 3 11:09 PM CDT)
- **Duration:** ~9 minutes
- **Error:** `LLM request timed out` — kimi-k2.6:cloud never responded
- **Fallback:** qwen3.5:9b — also timed out after ~9 minutes
- **Result:** No build artifacts, no skill files created, no progress

### BUILD-CUSTOM-SKILLS-1AM (1 AM CDT / 06:00 UTC)
- **Status:** ❌ FAILED
- **Session:** `2f65d166-a23d-400c-9e53-bf6d5864c4a0`
- **Started:** 2026-06-04 06:00 UTC (Jun 4 1:00 AM CDT)
- **Ended:** 2026-06-04 06:09 UTC (Jun 4 1:09 AM CDT)
- **Duration:** ~9 minutes
- **Error:** `LLM request timed out` — same pattern
- **Fallback:** qwen3.5:9b — also timed out
- **Result:** No build artifacts, no skill files created, no progress

---

## MONITOR-BUILD-JOBS Status (11:35 PM & 1:35 AM)
- **Status:** ❌ FAILED
- **Error:** `LLM request timed out` (last phase: model-call-started)
- **Pattern:** The monitor job itself cannot complete because the model hangs
- **Result:** No actual monitoring occurred — the monitor couldn't even start checking

---

## ERROR-WATCHDOG Status

**This watchdog is also broken.**

- **Job ID:** `e40a2803-30cf-4852-a272-9e456f29cb1d`
- **Schedule:** Every 5 minutes (`everyMs: 300000`)
- **Last run:** 2026-06-04 10:21 UTC (~5 min ago)
- **Status:** error
- **Consecutive errors:** 1 (current run), 40+ historically
- **Root cause:** Delivery misconfiguration — `announce` mode with no `to` target fails with:
  ```
  "Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>"
  ```

---

## Historical Failure Pattern (Last 5 Nights)

| Night | Voice Build | Custom Build | Monitor | Notes |
|-------|-------------|--------------|---------|-------|
| May 30→31 | ❌ Timeout | ❌ Timeout | ❌ Timeout | First failures |
| Jun 1→2 | ❌ Timeout | ❌ Timeout | ❌ Timeout | Pattern established |
| Jun 2→3 | ❌ Timeout | ❌ Timeout | ❌ Timeout | Continued |
| Jun 3→4 | ❌ Timeout | ❌ Timeout | ❌ Timeout | **No progress in 5 nights** |

---

## Root Cause Analysis

### Why ALL build jobs fail
1. **Model timeout:** Both jobs use `ollama/kimi-k2.6:cloud` as primary model
2. **The model hangs:** `model_call:started` but never produces content
3. **No fallback success:** Fallback chain (qwen3.5:9b) also fails with timeout
4. **Timeout:** Jobs abort after ~9 minutes (stuck session recovery)
5. **Zero output:** No code written, no files created, no progress

### Why ERROR-WATCHDOG fails
1. **Same model timeout issue** — the watchdog itself can't complete its check
2. **Delivery bug:** `announce` mode with no `--to` target for BlueBubbles
3. **Cascading failure:** The watchdog can't report its own failures

### Why MONITOR-BUILD-JOBS fails
1. **Same model timeout** — can't even begin monitoring
2. **Delivery bug** — same BlueBubbles issue when it does complete

---

## What This Means

- **No CODY builds have succeeded for 5 consecutive nights**
- **The voice skill project is completely stalled** — zero progress
- **The custom skills project is completely stalled** — zero progress
- **Green is NOT being notified** because delivery is broken
- **The watchdog is silently failing** every 5 minutes
- **Cron jobs are burning compute/time** with zero output

---

## Evidence from Cron Run Logs

### BUILD-VOICE-SKILL-Phase1 (last run):
```json
{
  "status": "error",
  "error": "LLM request timed out.",
  "sessionId": "e23dfd50-e731-439a-9bbe-ba077b1e7971",
  "durationMs": 546759,
  "model": "qwen3.5:9b"
}
```

### BUILD-CUSTOM-SKILLS-1AM (last run):
```json
{
  "status": "error",
  "error": "LLM request timed out.",
  "sessionId": "2f65d166-a23d-400c-9e53-bf6d5864c4a0",
  "durationMs": 547309,
  "model": "qwen3.5:9b"
}
```

### MONITOR-BUILD-JOBS (last run):
```json
{
  "status": "error",
  "error": "cron: job execution timed out (last phase: model-call-started)",
  "durationMs": 600741
}
```

---

## Recommendations

### Immediate Actions
1. **FIX DELIVERY** — Add explicit `to` target to all cron jobs, or change to `dm` mode
2. **FIX MODEL TIMEOUT** — Options:
   - Switch build jobs to local model (qwen3.5:9b is also failing, try gemma-2-9b?)
   - Increase timeout for cloud models
   - Run builds manually during active hours when models are responsive
3. **DISABLE BROKEN JOBS** — Stop the waste. These jobs have failed 20+ times with zero progress:
   ```bash
   # Disable the broken cron jobs until fixed
   openclaw cron disable 3955e592-a175-4050-8ad6-7ee96bb060b4  # BUILD-VOICE-SKILL
   openclaw cron disable 8cf77c91-5c37-44a8-b8c8-33a985f5d062  # BUILD-CUSTOM-SKILLS
   openclaw cron disable 75998c8b-52af-4e92-9955-4f606aa95d0f  # MONITOR-BUILD-JOBS
   openclaw cron disable e40a2803-30cf-4852-a272-9e456f29cb1d  # ERROR-WATCHDOG
   ```

### Strategic Actions
4. **Run builds manually** during active hours (9 AM - 6 PM CDT) when kimi-k2.6:cloud is responsive
5. **Consider retiring CODY** — If the agent can't build when scheduled, and the infrastructure can't support it, the scheduled build pattern is broken
6. **Re-evaluate the build strategy** — 30-minute overnight builds with unreliable models is not working

---

## Files Checked

- `/tmp/` — No build error logs found
- `~/.openclaw/cron/runs/*.jsonl` — Confirmed timeout pattern in all build jobs
- `~/.openclaw/cron/jobs-state.json` — Confirmed consecutive errors accumulating
- `~/.openclaw/skills/local-voice-streaming/` — Exists from May 31 build, NO new progress
- `~/.openclaw/workspaces/sol/skills/` — No new skills created since June 2

---

*Report generated by ERROR-WATCHDOG cron run at 2026-06-04 15:26 UTC*
*Watchdog job ID: e40a2803-30cf-4852-a272-9e456f29cb1d*
