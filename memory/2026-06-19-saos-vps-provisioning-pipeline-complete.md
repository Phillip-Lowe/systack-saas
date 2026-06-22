# SAOS VPS Provisioning Pipeline — Build Night Complete
**Date:** 2026-06-19 (Friday Build Night)  
**Session:** SOL + Phillip Lowe  
**Status:** ✅ PRODUCTION READY  
**Classification:** SYSTACK INTERNAL

---

## What Was Built

### Complete Infrastructure-as-Code Pipeline

| Component | File | Purpose |
|-----------|------|---------|
| **VPS Provisioner** | `scripts/provision_vps.py` | Vultr API client + cloud-init generator |
| **Provision Bridge** | `scripts/saos_provision_bridge.py` | Polls task_queue, auto-provisions VPS |
| **LaunchAgent** | `launchd/net.systack.saos-provision-bridge.plist` | Auto-starts bridge on boot |
| **Cloud-init** | Embedded in provision_vps.py | Two-stage boot (first install, second finalize) |
| **Webhook Workflow** | n8n: `yiMN48g5lFc7NpIm` | VPS ready notification handler |
| **PDF Documentation** | `docs/SAOS-VPS-Provisioning-Pipeline-v2.1.pdf` | Branded client deliverable |

---

## Critical Problems Solved

### Problem 1: cloud-init runcmd Skips Second Boot
**Root Cause:** cloud-init `runcmd` only executes once per instance. After reboot, the second boot skipped all finalization.
**Fix:** Moved second-stage logic to systemd one-shot service (`saos-second-stage.service`) that runs after reboot.
**Impact:** Would have left every VPS half-provisioned (services installed but not configured).

### Problem 2: Webhook Path Collision
**Root Cause:** `saos-provision` path was mapped to Stripe payment workflow expecting `checkout.session.completed` events. VPS ready payload caused `TypeError`.
**Fix:** Created dedicated `saos-vps-ready` webhook workflow for VPS completion notifications.
**Impact:** Would have crashed the provisioning pipeline on every VPS completion.

### Problem 3: Docker Group Membership
**Root Cause:** User added to docker group but membership requires re-login.
**Fix:** systemd service `saos-docker-group-fix` runs `sg docker -c "docker ps"` to activate membership.
**Impact:** Would have prevented `systack` user from running Docker containers.

### Problem 4: Log Loss on Reboot
**Root Cause:** `/var/log/` contents cleared on reboot.
**Fix:** Logs written to `/var/lib/cloud/instance/saas-provision.log` which persists.
**Impact:** Would have made debugging impossible for second-stage issues.

---

## Test Results

| Test | Status | Duration |
|------|--------|----------|
| VPS creation (Vultr API) | ✅ Pass | 2 min |
| First boot (cloud-init) | ✅ Pass | 3 min |
| Automatic reboot | ✅ Pass | 1 min |
| Second boot (systemd finalize) | ✅ Pass | 2 min |
| Model pull (qwen2.5:7b, 4.7 GB) | ✅ Pass | 1 min |
| Webhook to n8n | ✅ Pass | 200ms |
| Task queue update (DONE + instance ID) | ✅ Pass | Instant |
| Tailscale join | ✅ Pass | First boot |
| Ollama service | ✅ Pass | Second boot |
| Bridge polling automation | ✅ Pass | 30s interval |
| Auto-provision from task | ✅ Pass | 5s pickup |

**Total Pipeline**: 7-8 minutes from Stripe payment to VPS ready.

---

## Files Delivered

| File | Location | Size |
|------|----------|------|
| SAOS-VPS-Provisioning-Pipeline-v2.1.pdf | `docs/` + workspace | 306 KB |
| SAOS-VPS-Provisioning-Pipeline.md | `docs/` + workspace | 14 KB |
| provision_vps.py | `scripts/` | 9.5 KB |
| saos_provision_bridge.py | `scripts/` | 5.4 KB |

---

## Key Decisions

1. **systemd over cloud-init for second stage** — More reliable, runs on every boot.
2. **Separate webhooks for payment vs ready** — Prevents type mismatch crashes.
3. **Poll-based bridge over push** — Simpler, more resilient, easier to debug.
4. **16GB Business tier minimum** — qwen2.5:7b is 4.7 GB, leaves ~9GB headroom.
5. **LaunchAgent on macOS** — Matches existing Systack infra pattern.

---

## Known Issues

| Issue | Severity | Plan |
|-------|----------|------|
| OpenClaw install fails intermittently | Medium | 3 retries built-in; manual fallback |
| Tailscale Serve syntax unclear | Low | Direct Tailscale IP works; revisit later |

---

## Cost Economics

| Item | Monthly |
|------|---------|
| Vultr VPS (16GB) | $96 |
| Tailscale (free) | $0 |
| n8n (self-hosted) | $0 |
| Ollama (self-hosted) | $0 |
| **Cost to Serve** | **$96** |
| **SAOS Price** | **$299** |
| **Profit** | **$203 (68%)** |

---

## References

- **Repo:** github.com/Phillip-Lowe/systack-saas
- **Branch:** main (commit `bf6b31f`)
- **PDF:** `docs/SAOS-VPS-Provisioning-Pipeline-v2.1.pdf`
- **MD:** `docs/SAOS-VPS-Provisioning-Pipeline.md`
- **Workspace:** `/Users/philliplowe/.openclaw/workspaces/sol/`

---

*Built during Friday Build Night 2026-06-19. All test instances destroyed. Production pipeline validated and ready for client onboarding.*
