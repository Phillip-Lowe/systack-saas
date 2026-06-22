# Session — 2026-06-17 06:37 CDT

## Multi-Client Tailscale Architecture + RAM Verification

**User directive:** "Yes" (to updating build plan for multi-client architecture)

---

## What Was Built

### 1. Updated SAOS Build Plan
- **File:** `SAOS-PROVISIONING-BUILD-PLAN.md`
- **Added:** Multi-client Tailscale architecture section
- **Key insight:** Tagged devices don't count toward user limit

### 2. Updated VPS Provisioning Script
- **File:** `scripts/provision_vps.py`
- **Added:** Tailscale Serve + Funnel configuration in cloud-init
- **Auto-configures:** HTTPS public URL for client dashboard access

### 3. New: Multi-Client Tailscale Manager
- **File:** `scripts/tailscale-multi-client.py`
- **Purpose:** Manage unlimited clients without user seat limits

---

## RAM Verification: Is 16GB Enough?

### From Research (Ollama RAM Calculator)

| Model | Q4_K_M Size | Min VRAM | Status on 16GB VPS |
|-------|-------------|----------|-------------------|
| **qwen2.5:7b** | 4.4GB | 5.5GB | ✅ Comfortable (7GB headroom) |
| qwen2.5:14b | 8.7GB | 10GB | ⚠️ Tight (2GB headroom) |
| qwen2.5:32b | 18.8GB | 20GB | ❌ Won't fit |

### Total RAM Usage on 16GB VPS

| Component | RAM Usage |
|-----------|-----------|
| qwen2.5:7b (Q4_K_M) | ~4.4GB |
| OS + OpenClaw Gateway | ~1GB |
| n8n (Docker) | ~512MB |
| Tailscale + other services | ~256MB |
| **Total** | **~6.2GB** |
| **Available** | **~9.8GB** |

**VERDICT:** ✅ 16GB is sufficient for 7B model with comfortable headroom

**Note:** If client wants 14B upgrade, need 24GB VPS (~$192/mo)

---

## Tailscale Multi-Client Architecture

### The Problem
- Tailscale free tier = 6 users max
- 20 clients = 20 users = need paid plan

### The Solution: Tagged Devices
- Client VPS = **tagged device** (NOT user seat)
- Clients access via **HTTPS URL** (no account needed)
- Admin (you) = **1 user seat**

### Cost Projection

| Clients | User Seats | Tailscale Cost |
|---------|-----------|----------------|
| 5 | 1 | FREE |
| 10 | 1 | FREE |
| 20 | 1 | FREE |
| 50 | 1 | FREE |
| 100 | 1 | FREE |

**KEY:** Tagged devices are UNLIMITED on free tier

### Client Access Methods

| Method | Requires Account | Cost | Use Case |
|--------|-----------------|------|----------|
| **HTTPS URL** (Recommended) | No | Free | Dashboard access |
| Tailscale SSH | No* | Free | Server admin (you) |
| Tailscale App | Yes | $8/seat | Direct VPN access |

*SSH via auth keys, not user accounts

### Architecture

```
Your Tailnet (Systack)
├── You (1 user seat) ──────┐
├── Systack VPS (infra)     │
├── Client VPS #1 (tagged) ─┼──→ https://saos-123.tailnet.ts.net
├── Client VPS #2 (tagged) ─┘
└── ...
```

---

## Agent Capabilities on VPS

The SAOS agent on the VPS CAN:
- ✅ Send emails (SMTP, SendGrid)
- ✅ Process invoices (PDF parsing, data extraction)
- ✅ Handle bookings (database, API integrations)
- ✅ Send notifications (Slack, SMS, email)
- ✅ Web scraping (headless browser)
- ✅ API calls (Stripe, Square, Google Calendar)
- ✅ File processing (uploaded to VPS)
- ✅ Database operations (Postgres, SQLite)
- ✅ Schedule tasks (cron, n8n triggers)

The agent CANNOT (by design):
- ❌ Access client's local computer files
- ❌ Control client's mouse/keyboard
- ❌ See client's screen
- ❌ Access local network devices (printers, etc.)

**Workaround for local access:**
- Client uploads files to VPS via web UI
- Or use Tailscale to mount shared folders

---

## Files Committed

| File | Action |
|------|--------|
| `SAOS-PROVISIONING-BUILD-PLAN.md` | MODIFIED (multi-client section) |
| `scripts/provision_vps.py` | MODIFIED (Tailscale Serve config) |
| `scripts/tailscale-multi-client.py` | NEW |

**Commit:** `46a56e4` — "Add multi-client Tailscale architecture"
**Repo:** https://github.com/Phillip-Lowe/systack-saas.git

---

## Next Steps

1. **Get API keys:** Vultr, Tailscale (for programmatic access)
2. **Test real VPS:** Deploy first client with --tier test
3. **Verify Tailscale URL:** Check HTTPS access works
4. **Build client onboarding flow:** Welcome email → URL → dashboard

---

*Session saved 2026-06-17 06:37 CDT*
