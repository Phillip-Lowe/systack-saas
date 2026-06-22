# JURIS Agent Configuration — COMPLETE REFERENCE

**Fleet ID:** `juris`  
**Role:** Legal & Compliance Agent  
**Emoji:** ⚖️  
**Workspace:** `/Users/philliplowe/.openclaw/workspaces/juris`  
**Added:** 2026-06-17
**Status:** ACTIVE

---

## OpenClaw Config Block

Add this to `~/.openclaw/openclaw.json` in the `agents` array:

```json
{
  "id": "juris",
  "workspace": "/Users/philliplowe/.openclaw/workspaces/juris",
  "model": {
    "primary": "ollama/kimi-k2.6:cloud",
    "fallbacks": [
      "ollama/deepseek-v4-pro:cloud",
      "ollama/deepseek-v4-flash:cloud",
      "ollama/qwen3.5:9b"
    ]
  },
  "tools": {
    "profile": "research",
    "alsoAllow": ["web_search", "browser", "web_fetch"],
    "deny": ["exec", "message"]
  },
  "identity": {
    "avatar": "⚖️"
  }
}
```

---

## Tool Permissions

| Tool | Status | Reason |
|------|--------|--------|
| `web_search` | ✅ Allowed | Look up regulations, compliance standards |
| `browser` | ✅ Allowed | Access legal databases, government sites |
| `web_fetch` | ✅ Allowed | Download compliance documents |
| `read`/`write` | ✅ Allowed | Review contracts, write reports |
| `exec` | ❌ Denied | Security — no shell commands |
| `message` | ❌ Denied | Legal risk — no external messaging |

---

## Setup Commands

```bash
# 1. Create workspace directory
mkdir -p /Users/philliplowe/.openclaw/workspaces/juris

# 2. Add to openclaw.json agents array

# 3. Add "juris" to SOL's allowAgents list

# 4. Restart OpenClaw
openclaw gateway restart
```

---

## Also Update SOL's Subagents List

In `~/.openclaw/openclaw.json`, SOL's `allowAgents` must include `"juris"`:

```json
"allowAgents": [
  "geni",
  "cody",
  "pessi",
  "vali",
  "chatty",
  "assembly",
  "atlas",
  "juris"
]
```

---

## Fleet Context

JURIS is the 10th agent in the SAOS fleet:

**Core 7:** SOL 🛰️, VALI ✅, PESSI ⚠️, ORACLE 🔮, ATLAS 📚, ASSEMBLY 🛠️, JURIS ⚖️  
**Extended 3:** CODY 💻, CHATTY 💬, GENI 🎨

**System Loop:**
```
ORACLE → CODY → ASSEMBLY → VALI → PESSI → SOL → CHATTY → GENI → ATLAS → JURIS → [Loop]
```

JURIS reviews deployments for legal/compliance clearance before production.

---

## Orchestrator Integration

JURIS is registered in Postgres `agent_state`:
- Status: IDLE
- Capabilities: LEGAL, COMPLIANCE, CONTRACT, REVIEW, CLEAR
- Can claim tasks from `task_queue` with matching types

---

*Saved: 2026-06-17 05:43 CDT*  
*Source: User directive — "Say this everywhere I mean everywhere the wiki everything"*  
*File: `memory/2026-06-17-juris-agent-config-complete.md`*
