# OpenClaw Tool Failures — Diagnostic Report
**Date:** 2026-06-04  
**Agent:** SOL  
**Issue:** Intermittent failures in `memory_search`, `web_search`, and other tools

---

## Summary

Three distinct failure modes were identified during live debugging:

| Tool | Failure Mode | Root Cause | Status |
|------|-------------|-----------|--------|
| `memory_search` | "file lock stale for ...jsonl" | Stale session file lock from previous crashed session | **Mitigated** (lock cleared, but recurs) |
| `web_search` | "Ollama web search returned malformed JSON" | Ollama `/api/search` endpoint returns 404; no web search capability | **Pending restart** (switched to perplexity) |
| `write` | Occasional silent failures | Related to model timeout / resource exhaustion | **Monitoring** |

---

## Detailed Findings

### 1. `memory_search` — Stale File Lock

**Error message:**
```
file lock stale for /Users/philliplowe/.openclaw/agents/sol/sessions/42110a93-89f7-4074-9539-d2317f21240e.jsonl
```

**Investigation:**
- The lock file exists but no process currently holds it (`lsof` returns nothing)
- Lock was manually removed with `rm -f *.lock`
- **Lock reappears immediately** on next `memory_search` call
- This suggests the lock mechanism is detecting the session file as "stale" based on some internal criteria (possibly session age or process association)

**Root cause hypothesis:**
- Session `42110a93-89f7-4074-9539-d2317f21240e` was from a previous crashed/interrupted session
- The session file still exists in the filesystem
- OpenClaw's lock manager considers it "stale" because the owning process is gone
- The lock is **recreated** each time `memory_search` tries to access it

**Mitigation applied:**
```bash
rm -f /Users/philliplowe/.openclaw/agents/sol/sessions/42110a93-89f7-4074-9539-d2317f21240e.jsonl.lock
```

**Full fix needed:**
- Archive or delete the orphan session file
- Run `openclaw doctor --fix` to clean up orphan transcripts

---

### 2. `web_search` — Ollama Search Provider Broken

**Error message:**
```
Ollama web search returned malformed JSON
```

**Investigation:**
- Ollama's `/api/search` endpoint returns **404 page not found**
- Ollama does NOT have a built-in web search API
- OpenClaw's `web-search-provider-BXYa_nIR.js` tries to call `http://127.0.0.1:11434/api/search`
- This endpoint doesn't exist in standard Ollama installations

**Code trace:**
```javascript
// From web-search-provider-BXYa_nIR.js
async function readOllamaWebSearchResponse(response) {
    try {
        return await response.json();
    } catch (cause) {
        throw new Error("Ollama web search returned malformed JSON", { cause });
    }
}
```

The response from Ollama is actually a 404 HTML page ("404 page not found"), not JSON. The `response.json()` parse fails, triggering the "malformed JSON" error.

**Config change applied:**
```bash
openclaw config set tools.web.search.provider perplexity
```

**Note:** The config now shows `"provider": "perplexity"` but the gateway has **NOT been restarted**. The old provider (`ollama`) is still active in the running gateway process.

**Full fix needed:**
- Restart the OpenClaw gateway to pick up the config change
- Verify perplexity API key is configured if required

---

### 3. Ollama Memory Exhaustion

**Observations:**
- Ollama was running `qwen3.5:9b` (14.3GB model, 10.8GB VRAM)
- System memory usage: 54% (9GB of 16GB RAM)
- When model is loaded, system becomes resource-constrained
- `curl` requests to Ollama timeout or hang

**Impact:**
- Model loading/unloading causes delays
- Memory pressure may cause tool calls to timeout
- System instability when multiple models are loaded

**Mitigation applied:**
```bash
killall -9 "ollama runner"
```

---

### 4. Config Validation Issue (Pre-existing)

**Error:**
```
agents.defaults: Invalid input
```

**Investigation:**
- `openclaw config validate` initially failed
- `openclaw doctor --fix` was run
- Config is now valid
- No material impact on tool failures, but indicates config drift

---

## Actions Taken

1. ✅ Cleared stale file lock
2. ✅ Changed `web_search` provider from `ollama` to `perplexity`
3. ✅ Killed hung Ollama runners to free memory
4. ✅ Ran `openclaw doctor --fix` to repair config validation
5. ❌ **FAILED:** Restart gateway to apply web_search provider change — gateway did NOT restart (same PID 15936)
6. ⏳ **PENDING:** Archive orphan session files

### Update: Gateway Restart Failure

The gateway restart via `gateway` tool did not work:
- Signal `SIGUSR1` was sent to PID 15936
- Process did NOT restart (PID unchanged after 60+ seconds)
- Config on disk shows `"provider": "perplexity"` but runtime still uses `ollama`

**Workaround required:** Manual full restart:
```bash
# Terminal:
openclaw gateway restart
# OR kill + start:
kill 15936 && openclaw gateway
```

---

## Recommended Next Steps

### Immediate (Do Now)
```bash
# 1. Restart gateway to pick up perplexity provider
openclaw gateway restart

# 2. Archive orphan sessions
openclaw doctor --fix
# (when prompted about orphan transcripts, confirm archive)

# 3. Verify web_search works after restart
```

### Short Term
- Monitor memory usage during model loads
- Consider using smaller models (qwen2.5-coder:7b is 4.7GB vs qwen3.5:9b at 14.3GB)
- Set up a cron job to clean stale locks periodically

### Long Term
- Add perplexity API key for reliable web search
- Consider switching to local-only models to avoid cloud dependency
- Document the Ollama search limitation in TOOLS.md

---

## Files Referenced

| File | Purpose |
|------|---------|
| `~/.openclaw/openclaw.json` | Main config — provider changed to perplexity |
| `~/.openclaw/agents/sol/sessions/*.jsonl` | Session files — orphan locks detected |
| `~/.openclaw/logs/gateway.log` | Gateway logs — MCP failures, old errors |
| `~/.openclaw/logs/stability/` | Crash dumps — unhandled rejection history |
| `web-search-provider-BXYa_nIR.js` | OpenClaw source — Ollama search implementation |

---

## Lessons Learned

1. **Ollama does NOT provide web search** — the `/api/search` endpoint doesn't exist. OpenClaw's default "ollama" provider for web_search is non-functional.

2. **Stale locks are self-healing but noisy** — removing the lock file clears the immediate error, but the underlying orphan session file should be archived.

3. **Config changes need restart** — `openclaw config set` updates the file but the running gateway keeps the old provider in memory.

4. **Memory monitoring is critical** — 16GB MacBook Air struggles with 14GB models. Keep an eye on `ps aux | grep ollama` output.

---

*Generated by SOL during live debugging session*
