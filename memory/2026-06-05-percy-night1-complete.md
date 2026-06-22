# Percy Deployment — Night 1 Complete

**Date:** 2026-06-04 → 2026-06-05 (overnight session)
**Time:** ~8 hours of deployment work
**Status:** Infrastructure complete, model functional, pending 8GB upgrade

---

## What We Accomplished

✅ Vultr VPS deployed (Chicago, 4GB)
✅ Ollama installed with qwen2.5 models (7b, 3b, 1.5b, 3b-4k)
✅ OpenClaw Gateway installed and configured
✅ Tailscale installed on VPS + Jacqueline's phone + laptop
✅ Tailscale Serve active (HTTPS URL working)
✅ Device pairing and approval working
✅ Percy identity files created (SOUL.md, AGENTS.md, USER.md, etc.)
✅ Both devices (phone + laptop) can access the Control UI
✅ Percy responds to messages (with delay due to RAM)

---

## The Core Problem Discovered

**4GB VPS cannot run OpenClaw + identity files + model comfortably.**

The system prompt (identity files + OpenClaw bootstrap) is ~1,250 tokens.
With 4K context: leaves almost nothing for conversation → context overflow.
With 16K context: 3B model uses 3.1GB RAM → system swaps → 2+ min timeouts.

**Solution: 8GB VPS minimum for production.**

---

## Files Created Tonight

| File | Purpose |
|------|---------|
| PERCY-DEPLOYMENT-PLAN.md | Full deployment steps |
| DEPLOYMENT-PLAYBOOK.md | Troubleshooting guide |
| MODEL-CONTEXT-SIZING-GUIDE.md | RAM/context math for all VPS sizes |
| FINAL-WORKING-CONFIG.md | Exact working config JSON |
| STATUS-SUMMARY.md | Current status for Jacqueline |
| JACQUELINE-NOTE.txt | Simple instructions for Jacqueline |
| percy-workspace/*.md | Identity files (SOUL, AGENTS, USER, etc.) |

---

## Key Decisions Made

1. **Use Tailscale Serve from the start** — no HTTP workarounds
2. **No password auth** — Tailscale IS the security
3. **3B-4K model for 4GB demo** — functional but slow
4. **8GB upgrade for production** — 7B/32K model, full speed
5. **Strip identity files to <1000 words** for 4GB, restore for 8GB
6. **Phone first, laptop second** — phone testing avoids Windows issues
7. **Document everything** — this is the template for all future clients

---

## Costs

- Vultr VPS 4GB: $20/mo (demo)
- Vultr VPS 8GB: $40/mo (production)
- Tailscale: Free
- OpenClaw: Free
- Ollama: Free
- Systack setup: Free (beta/testimonial)

---

## Next Session (Tomorrow)

1. Jacqueline wakes up, checks Vultr email
2. Phillip logs into Vultr, upgrades to 8GB
3. Switch to qwen2.5:7b with 32K context
4. Restore full identity files
5. Test response time (<10 seconds target)
6. Train Jacqueline on Percy capabilities
7. Set first reminder/schedule task
8. Gather feedback, adjust

---

## Emotional State

Long night. Many failures. But we learned.

The deployment is 90% done. The infrastructure works. The model responds. The only blocker is RAM, which is a known, fixable problem.

Jacqueline will be happy once it's upgraded. For now, she has a slow but functional AI assistant. That's more than most people have.

---

*Logged by Sol*
*Goodnight*
