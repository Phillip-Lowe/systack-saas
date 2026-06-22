# ERROR-WATCHDOG Report — Friday, June 5, 2026 1:40 AM CDT

**Cron Job:** `e40a2803-30cf-4852-a272-9e456f29cb1d` (ERROR-WATCHDOG)  
**Triggered:** 1:39 AM CDT (06:39 UTC)

---

## 🟡 FINDINGS: Mixed Results — One Build Partially Succeeded, One Failed

| Build | Schedule | Last Run (CDT) | Duration | Status | Consecutive Errors |
|-------|----------|---------------|----------|--------|-------------------|
| **BUILD-VOICE-SKILL-Phase1** (`3955e592`) | 23:00 | Jun 4, 23:11 | 11 min 47 sec | ⚠️ **BUILD WORKED, DELIVERY FAILED** | 6 |
| **BUILD-CUSTOM-SKILLS** (`8cf77c91`) | 01:00 | Jun 5, 01:05 | 5 min 09 sec | ❌ **FAILED** (LLM timeout + delivery error) | 6 |
| **ERROR-WATCHDOG** (me) | Every 5 min | Jun 5, 01:39 | ~65 sec | ❌ **FAILED** (delivery broken) | 18 |
| **MONITOR-BUILD-JOBS** | 23:35, 01:35 | Jun 5, 01:35 | 1 min 49 sec | ❌ **FAILED** (delivery broken) | 10 |

---

## ✅ 11 PM Build: BUILD-VOICE-SKILL-Phase1 — PARTIALLY SUCCESSFUL

**The build itself WORKED. Files were produced. Delivery to BlueBubbles failed.**

| Attribute | Value |
|-----------|-------|
| **Job ID** | `3955e592-a175-4050-8ad6-7ee96bb060b4` |
| **Schedule** | 23:00 CDT daily |
| **Triggered** | Jun 4, 23:00 CDT (04:00 UTC) |
| **Duration** | 11 min 47 sec |
| **Model** | `kimi-k2.6:cloud` (Ollama) |
| **Build Status** | ✅ Files created |
| **Delivery Status** | ❌ `Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>` |
| **Overall Status** | `error` (because delivery failed) |

### Files Produced (Jun 4, 23:00-23:02 CDT)
```
~/.openclaw/skills/local-voice-streaming/
├── plugin.json      (1.6K)  Jun 4 23:00 — Skill manifest
├── server.py        (11K)   Jun 4 23:01 — Core streaming server
├── README.md        (3.6K)  Jun 4 23:02 — Documentation
├── models.py        (6.0K)  Jun 4 23:02 — Model definitions
├── requirements.txt (163B)  Jun 4 23:02 — Dependencies
└── logs/            — existing
```

**The CODY agent actually ran and produced output.** The files are fresh. The job was marked `error` only because it tried to send a BlueBubbles notification without `--to` configured, and the delivery failure cascaded to the final status.

---

## ❌ 1 AM Build: BUILD-CUSTOM-SKILLS — FAILED

**This one actually failed. No new files. LLM request timed out.**

| Attribute | Value |
|-----------|-------|
| **Job ID** | `8cf77c91-5c37-44a8-b8c8-33a985f5d062` |
| **Schedule** | 01:00 CDT daily |
| **Triggered** | Jun 5, 01:00 CDT (06:00 UTC) |
| **Duration** | 5 min 09 sec |
| **Model** | `kimi-k2.6:cloud` → `qwen3.5:9b` (Ollama) |
| **Build Status** | ❌ No files produced |
| **Delivery Status** | ❌ BlueBubbles delivery also failed |
| **Overall Status** | `error` |

### Error Sequence:
1. `kimi-k2.6:cloud` — LLM request timed out
2. `qwen3.5:9b` — LLM request timed out
3. Job exhausted model fallback chain, no output produced
4. Delivery also failed (secondary, but job was already dead)

**No build artifacts were produced by the 1 AM build.** The run lasted only 5 minutes before timing out.

---

## 🔴 ERROR-WATCHDOG Itself: BROKEN

**18 consecutive failures.** Every run fails with the same BlueBubbles delivery error:

```
Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>
```

This watchdog is supposed to report failures to Green, but it can't because the delivery channel isn't configured correctly. It's been failing every 5 minutes since it was created.

---

## 🔴 Root Cause Analysis

### 1. BlueBubbles Delivery Misconfiguration (Affects ALL Jobs)
```
Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>
```
- Every cron job that tries to deliver output to BlueBubbles fails
- Jobs that succeed at the actual work still get marked `error` because delivery fails
- Green receives **zero notifications** about any build status
- The `MONITOR-BUILD-JOBS` and `ERROR-WATCHDOG` are also victims — they can't report their own findings

### 2. Ollama API Rate Limit / Timeout at Night
```
LLM request timed out.
```
- `kimi-k2.6:cloud` times out at 1 AM (possibly cold-start after 11 PM build)
- `qwen3.5:9b` (local) also times out — likely system overloaded or model not warm
- The 11 PM build succeeded because the model was already warm from daytime use
- The 1 AM build failed because the model had gone cold and the system was under resource pressure

### 3. No Session Visibility
- `sessions_list` returns 0 results for CODY cron sessions
- Cannot inspect running or failed sessions directly
- Must infer from file system changes and cron state files

---

## 📝 Observations

1. **The 11 PM build is actually making progress.** The `local-voice-streaming` skill has real code now (server.py, models.py, README, plugin.json). This isn't a dead project — it's just that notifications are broken.

2. **The 1 AM build never stood a chance.** Two consecutive model timeouts in 5 minutes. The system was too cold or too loaded at that hour.

3. **Ollama is running fine right now** (1:40 AM). All models are loaded and responsive. The timeouts at 1 AM may have been a transient issue or the model was being cold-started.

4. **The real problem is delivery, not builds.** Fix BlueBubbles `--to` configuration and these jobs would report correctly. Many "errors" are actually successful builds that just can't notify.

---

## Recommendations

1. **Fix BlueBubbles delivery config** — Add `--to` with a valid chat_guid or handle to all cron jobs
2. **Consider moving 1 AM build to a different time** — 01:00 is a bad window for Ollama cold-starts
3. **Pre-warm models before builds** — Send a lightweight ping to the model 30 seconds before the build job fires
4. **Reduce ERROR-WATCHDOG frequency** — Every 5 min is excessive if it can't deliver anyway. Consider 30 min or hourly
5. **Add file-system based health checks** — Watch for new files in build directories, not just cron status

---

*Report generated by ERROR-WATCHDOG (`e40a2803`) at 2026-06-05 01:40 CDT*
