# Session — 2026-06-17 05:51 CDT

## SAOS Repo Separation + Dashboard Fix

**User directive:** "Save this in all places" + "SAOS dashboard is down"

---

## What Happened

1. **Repo Separation:** Moved SAOS code from `utopia-deli-order` to new `systack-saas` repo
2. **Gateway Restart:** User restarted OpenClaw gateway
3. **Dashboard Down:** `orchestrator-daemon.py` was running (PID 70691) but dashboard API (port 8765) was dead
4. **Root Cause:** Files moved to `/tmp/systack-saas-init/` — running from wrong location

---

## Fix Applied

**Dashboard restarted from systack-saas repo:**
```bash
cd /tmp/systack-saas-init/dashboard
mkdir -p ../logs
nohup python3 api.py --port 8765 >../logs/dashboard-api.log 2>&1 &
```

**Status:** ✅ `{"service":"saos-dashboard-api","status":"ok"}`

---

## Repo Status

| Repo | URL | Status |
|------|-----|--------|
| **systack-saas** | https://github.com/Phillip-Lowe/systack-saas | ✅ SAOS orchestrator, dashboard, fleet |
| **systack-site** | https://github.com/Phillip-Lowe/systack-site | ✅ Systack website (embedded in workspace) |
| **utopia-deli-order** | https://github.com/Phillip-Lowe/utopia-deli-order | ✅ Deli code only (SAOS removed) |

---

## Active Processes

| Service | PID | Status |
|---------|-----|--------|
| Orchestrator Daemon | 70691 | ✅ Running (launchd) |
| Dashboard API | (new) | ✅ Just restarted |
| n8n | (managed) | ✅ Running |

---

## Note for Future Sessions

**After gateway restart:**
1. Orchestrator daemon auto-restarts via launchd ✅
2. Dashboard API does NOT auto-restart — must restart manually
3. Dashboard files now live in `systack-saas` repo, not workspace

**Dashboard restart command:**
```bash
cd /path/to/systack-saas/dashboard
python3 api.py --port 8765
```

---

*Saved: 2026-06-17 05:53 CDT*  
*User directive: "Save this in all places"*

## Dashboard LaunchAgent Added

**File:** `~/Library/LaunchAgents/net.systack.dashboard.plist`
**Status:** ✅ Loaded (PID 97098)
**Port:** 8765
**Auto-restart:** Yes (KeepAlive enabled)

### Config
- Label: `net.systack.dashboard`
- Command: `python3 /tmp/systack-saas-init/dashboard/api.py --port 8765`
- RunAtLoad: true
- KeepAlive: true
- Logs: `/tmp/systack-saas-init/logs/dashboard.log`

### Verification
```bash
launchctl list | grep systack
# Shows: 97098 0 net.systack.dashboard
```

### Active LaunchAgents
| Service | PID | Label |
|---------|-----|-------|
| Orchestrator | 70691 | `net.systack.orchestrator` |
| Dashboard | 97098 | `net.systack.dashboard` |
| Morning Briefing | - | `com.systack.morning-briefing` |
| Invoice API | 86824 | `com.systack.invoice-api` |


## Invoice Dashboard Restart + LaunchAgent

**Status:** ✅ Fixed and auto-restarting

### Problem
- Invoice dashboard (port 8766) was down after gateway restart
- `invoice_dashboard_api.py` serves invoice data from SQLite DB
- Not previously managed by launchd

### Fix
```bash
cd /Users/philliplowe/.openclaw/workspaces/sol
python3 invoice_dashboard_api.py  # Port 8766
```

### LaunchAgent Added
**File:** `~/Library/LaunchAgents/net.systack.invoice-dashboard.plist`
**Label:** `net.systack.invoice-dashboard`
**Port:** 8766
**PID:** 97487
**Auto-restart:** Yes (KeepAlive)

### Verification
```bash
curl -s http://localhost:8766/api/summary
# Response: {"total_invoices": 95, ...}
```

### All Active Systack Services
| Service | PID | Port | LaunchAgent |
|---------|-----|------|-------------|
| Orchestrator | 70691 | - | `net.systack.orchestrator` |
| Dashboard | 97098 | 8765 | `net.systack.dashboard` |
| Invoice Dashboard | 97487 | 8766 | `net.systack.invoice-dashboard` |
| Invoice API | 86824 | 9001 | `com.systack.invoice-api` |
| Morning Briefing | - | - | `com.systack.morning-briefing` |

