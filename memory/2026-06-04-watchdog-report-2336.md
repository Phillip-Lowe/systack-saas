# ERROR-WATCHDOG Report — June 4, 2026 11:36 PM CDT

## Executive Summary

**CRITICAL: All CODY build sessions FAILED again tonight (June 4). Same dual failure pattern continues for the 6th consecutive night.**

| Job | Schedule | Last Run | Status | Consecutive Errors |
|-----|----------|----------|--------|-------------------|
| BUILD-VOICE-SKILL-Phase1 | 23:00 CDT (11 PM) | Jun 4 23:11 | ❌ **FAILED** (delivery + timeout) | 6 |
| MONITOR-BUILD-JOBS | 23:35, 01:35 | Jun 4 23:36 | ❌ **FAILED** (watchdog broken) | 9 |
| ERROR-WATCHDOG | Every 5 min | Jun 4 23:35 | ❌ **FAILED** (delivery broken) | 16 |

**Root causes (unchanged):**
1. **BlueBubbles delivery misconfiguration** — All jobs fail with `Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>`
2. **Ollama API rate limit exhaustion** — Weekly quota exceeded, causing LLM timeouts

---

## Build Job Results (June 4 → June 5)

### BUILD-VOICE-SKILL-Phase1 (11 PM CDT / 04:00 UTC)
- **Status:** ❌ FAILED (delivery error)
- **Session:** `d0423a89-3b02-45e8-a39d-a4076deec10a`
- **Started:** 2026-06-04 23:00 CDT (04:00 UTC)
- **Ended:** 2026-06-04 23:11 CDT (04:11 UTC)
- **Duration:** ~11 minutes
- **Model used:** `kimi-k2.6:cloud` (ollama)
- **Usage:** 984,927 input tokens, 8,448 output tokens
- **Error:** `Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>`

**What happened:** The build session actually RAN and PRODUCED OUTPUT:
- Created/updated files in `~/.openclaw/skills/local-voice-streaming/`
- Qwen3 8B model downloaded and warmed (~7s first token)
- **BUT** delivery failed → Green never notified
- Job marked as `error` due to delivery failure, not build failure

**Blockers from build output:**
- **STT (Parakeet)** — Download killed by SIGTERM (macOS resource limit on 2GB model)
- **TTS (Kokoro)** — Downloaded but inference is placeholder (MLX Kokoro API still settling)

### 1 AM Build Session
- **Status:** ❌ **NO EVIDENCE OF EXECUTION**
- No active session found in cron runs for the 1 AM slot
- The MONITOR-BUILD-JOBS run at 01:44 reported LLM timeout
- No build artifacts created after 23:11

---

## Error Log Analysis

### OpenClaw Gateway Log (`/tmp/openclaw/openclaw-2026-06-04.log`)
- **Voice skill session:** Spawned at 23:00 CDT (`sessionId=d0423a89-3b02-45e8-a39d-a4076deec10a`)
  - Prep took 30.3 seconds (mostly bundle-tools: 30.2s)
  - Session became stream-ready at 23:00:30
- **Build job failure:** Reported at 23:11:46 with delivery error
- **Watchdog failure:** Same delivery error at 23:35:27 and 23:36:18

### Cron Run Logs
- `~/.openclaw/cron/runs/3955e592-a175-4050-8ad6-7ee96bb060b4.jsonl` — 6 consecutive errors
- `~/.openclaw/cron/runs/75998c8b-52af-4e92-9955-4f606aa95d0f.jsonl` — 9 consecutive errors
- `~/.openclaw/cron/runs/e40a2803-30cf-4852-a272-9e456f29cb1d.jsonl` — Not found (watchdog may not log separately)

### Stuck Sessions
- Historical sessions `e23dfd50-e731-439a-9bbe-ba077b1e7971` and `d5fce8fc-7a38-474e-bfcf-d5fce8fc7a38` reported as "No session found" at 22:35
  - These are stale references from previous nights
  - Not active tonight

---

## Infrastructure Issues

### n8n MCP Connections (Persistent)
| Server | Status | Error |
|--------|--------|-------|
| n8n-mcp (systack.net) | ❌ FAIL | `Unauthorized: Invalid authorization header format - Missing Bearer prefix` |
| n8n (localhost:5678) | ❌ FAIL | `Cannot POST /mcp` |
| n8n-cloud (theutopiadeli.com) | ❌ FAIL | `Connection timed out after 30000ms` |

These are unrelated to build failures but indicate MCP infrastructure issues.

---

## Comparison: Previous Nights

| Night | Voice Build | Custom Skills | Monitor | Watchdog | Root Cause |
|-------|-------------|---------------|---------|----------|------------|
| May 31 | Not executed | N/A | Error (delivery) | Broken | No spawn configured |
| Jun 1 | Partial (code only) | N/A | Error (timeout) | Broken | LLM timeout |
| Jun 2 | Timeout | Timeout | Error (timeout) | Broken | Rate limit |
| Jun 3 | Timeout | Timeout | Error (timeout) | Broken | Rate limit |
| **Jun 4** | **Error (delivery)** | **No evidence** | **Error (delivery)** | **Broken** | **Delivery + rate limit** |

---

## Recovery Actions Needed

### IMMEDIATE (Tonight)
1. **Fix BlueBubbles delivery target** — All cron jobs need explicit `--to` handle or chat_guid
2. **Verify build artifacts** — Check if voice skill files were actually updated tonight

### SHORT TERM (Tomorrow)
3. **Fix Ollama API rate limit** — Weekly quota resets; monitor usage
4. **Fix n8n MCP auth** — Update Bearer token format for systack.net
5. **Fix STT download** — Use `huggingface-cli download` manually for Parakeet 2GB model
6. **Fix TTS inference** — Wait for MLX Kokoro API to stabilize or find workaround

### MEDIUM TERM
7. **Rebuild watchdog with proper delivery config**
8. **Add fallback notification channel** (not just BlueBubbles)
9. **Consider local-only models** to avoid cloud rate limits

---

## Files Created/Updated Tonight

Based on build session output:
- `~/.openclaw/skills/local-voice-streaming/plugin.json` ✅
- `~/.openclaw/skills/local-voice-streaming/server.py` ✅
- `~/.openclaw/skills/local-voice-streaming/models.py` ✅
- `~/.openclaw/skills/local-voice-streaming/README.md` ✅
- `~/.openclaw/skills/local-voice-streaming/requirements.txt` ✅

---

*Report generated by ERROR-WATCHDOG (cron:e40a2803-30cf-4852-a272-9e456f29cb1d)*
*Next scheduled check: 11:41 PM CDT (if watchdog not in error backoff)*
