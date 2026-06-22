# Percy Client Deployment — Day 1 Log

**Date:** 2026-06-04 (night)
**Client:** Jacqueline, McDonald's General Manager
**Deployer:** Phillip Lowe (Systack)
**Agent:** Sol (assisted by Copilot)

---

## What Happened Tonight

### 21:00 — Deployment Started
- Created Percy deployment plan
- Vultr VPS deployed: 66.42.121.145 (Chicago, 4GB)
- OpenClaw installed, configured with password auth
- Firewall opened for port 18789

### 21:30 — First Client Test
- Jacqueline tried to access from phone
- Got "Secure browser context required" error
- Tried `allowInsecureAuth: true` — didn't work with password auth
- Realized: browsers REQUIRE HTTPS for WebSocket connections

### 22:00 — Tailscale Attempt
- Installed Tailscale on VPS
- Jacqueline installed on phone (worked)
- Windows laptop: Windows S mode blocked install
- Had to switch out of S mode

### 22:30 — HTTPS via Tailscale Serve
- Enabled HTTPS in Tailscale admin console
- Started `tailscale serve --bg http://localhost:18789`
- Got HTTPS URL: `https://percy-mcdonalds.taildd162e.ts.net/`
- Phone connected, but needed device approval

### 22:45 — Device Approval Loop
- Approved Jacqueline's phone device
- Gateway crashed multiple times due to config mismatch
- Discovered: `--bind lan` in systemd overrides `bind: loopback` in config
- Fixed systemd service file (removed --bind flag)
- Gateway now stable

### 23:00 — Final Working State
- Gateway: running, connectivity probe OK
- Tailscale Serve: HTTPS proxy active
- Phone: can access, device approved
- Jacqueline sent first message: "can we tell percy to do this?"
- Percy DID NOT RESPOND — gateway was in restart loop at exact moment

---

## Critical Errors Made

1. **Didn't use Tailscale Serve from the start**
   - Wasted 30+ min trying HTTP workarounds
   - `allowInsecureAuth` is a red herring with password auth
   
2. **Systemd service had wrong args**
   - `--bind lan` caused config mismatch
   - Caused scope upgrade loop, device re-pairing
   
3. **Heredoc over sshpass corrupted files**
   - Service file got mangled multiple times
   - Had to write locally, SCP, or use printf
   
4. **Didn't check Windows S mode first**
   - Jacqueline wasted time trying to install Tailscale
   - Windows silently blocked the installer
   
5. **Didn't test full chain before client**
   - Client tried during gateway restart loop
   - Percy appeared "unresponsive" but was actually crashed

---

## What Worked

1. **VPS deployment was smooth** — AlmaLinux 8, Ollama, qwen2.5:7b all installed cleanly
2. **Tailscale on VPS worked first try** — just needed HTTPS enabled in admin
3. **Phone browser worked** — once HTTPS was available
4. **Password auth is simple** — no tokens to manage for client
5. **Doctor --fix recovered config** — when we broke it, recovery was automatic

---

## Client Status (End of Day 1)

| Component | Status |
|-----------|--------|
| VPS | ✅ Running |
| Ollama qwen2.5:7b | ✅ Loaded |
| OpenClaw Gateway | ✅ Running, connectivity OK |
| Tailscale Serve | ✅ HTTPS active |
| Jacqueline Phone | ✅ Can access URL |
| Jacqueline Windows | ❌ S mode issue, Tailscale not installed |
| Percy AI responses | ⚠️ Untested — need client to retry |
| Identity files | ⚠️ Template only, not customized |

---

## Next Steps (Day 2)

1. **Have Jacqueline retry from phone** — send "Hi Percy, I'm Jacqueline"
2. **Verify Percy responds with AI-generated message**
3. **Customize Percy identity** — fill in USER.md with her actual info
4. **Fix Windows laptop** — exit S mode, install Tailscale
5. **Document her preferences** — quiet hours, schedule, priorities
6. **Explain Tier 0 capabilities** — what Percy can/cannot do
7. **Set up first proactive reminder** — e.g., "check email at 8am"

---

## Files Created Tonight

- `clients/mcdonalds-gm/PERCY-DEPLOYMENT-PLAN.md`
- `clients/mcdonalds-gm/DEPLOYMENT-PLAYBOOK.md`
- `clients/mcdonalds-gm/percy-workspace/` (SOUL.md, AGENTS.md, USER.md, MEMORY.md, TOOLS.md, HEARTBEAT.md, KUDU-7.md)
- Updated `MEMORY.md` with client deployment template

---

## Key Insight

**Deploying an AI agent for a non-technical client is 80% infrastructure, 20% AI.**

The hard part isn't the model or the prompts — it's:
- Network connectivity (Tailscale, HTTPS)
- Device compatibility (Windows S mode, browser security)
- Authentication (password vs token, device pairing)
- Client education (how to open a URL, what "connected" means)

**For future clients: Always test the FULL chain before announcing it's ready.**

---

*Logged by Sol*
*For Percy, for MEMORY.md, for all future deployments*
