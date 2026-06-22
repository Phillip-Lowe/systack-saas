# Session — 2026-06-17 06:18 CDT

## Vultr VPS Provisioning Integration

**User directive:** Build Vultr API integration for SAOS provisioning pipeline

---

## What Was Built

### 1. Vultr Provisioning Script
- **File:** `scripts/provision_vps.py`
- **Status:** ✅ Created, tested, committed
- **Location:** `/tmp/systack-saas-init/scripts/provision_vps.py` (systack-saas repo)

**Features:**
- Full Vultr API v2 integration (create, list, get, destroy instances)
- Cloud-init generation for automated client VPS setup
- Tier-based plan selection (business/enterprise/test)
- Tailscale auto-join with hostname tagging
- Ollama installation + model pull
- Docker, n8n, OpenClaw base setup
- UFW firewall + fail2ban security
- Webhook callback to n8n on completion
- Test mode for safe development

### 2. Test Suite
- **File:** `scripts/test_provision.py`
- **Tests:** 7 passing
- Coverage: Cloud-init generation, API mocking, tier configs

### 3. Cloud-init Template
**Installed on new VPS:**
- Ubuntu 22.04 LTS (OS ID 1743, same as Percy deployment)
- Tailscale (auto-joins Systack tailnet)
- Ollama (pulls qwen2.5:7b)
- Docker + Docker Compose
- OpenClaw (attempts install)
- Nginx (reverse proxy ready)
- UFW firewall (ports 22, 80, 443, 5678, 11434, 18789)
- fail2ban (intrusion protection)
- systack user (docker + sudo)
- Webhook callback to n8n provisioning pipeline

---

## VPS Specifications

| Tier | Plan | Specs | Monthly Cost | Region |
|------|------|-------|-------------|--------|
| **Business** | vhp-4c-16gb | 4 vCPU, 16GB RAM | ~$96/mo | ord (Chicago) |
| **Enterprise** | vhp-4c-16gb | 4 vCPU, 16GB RAM + support | ~$96/mo + | ord (Chicago) |
| **Test** | vc2-1c-1gb | 1 vCPU, 1GB RAM | ~$5/mo | ord (Chicago) |

---

## Usage

### Create a client VPS (test mode)
```bash
python3 scripts/provision_vps.py \
  --client-id CLIENT001 \
  --tier business \
  --email "client@example.com" \
  --agent-name "Percy" \
  --test-mode
```

### Create for real (requires API keys)
```bash
export VULTR_API_KEY="your-vultr-api-key"
export TAILSCALE_AUTH_KEY="tskey-auth-..."

python3 scripts/provision_vps.py \
  --client-id CLIENT001 \
  --tier business \
  --email "client@example.com" \
  --agent-name "Percy"
```

### List SAOS instances
```bash
python3 scripts/provision_vps.py --list --api-key "$VULTR_API_KEY"
```

### Destroy instance
```bash
python3 scripts/provision_vps.py --destroy "instance-id" --api-key "$VULTR_API_KEY"
```

---

## Integration Points

### With n8n Provisioning Pipeline
The cloud-init script calls back to n8n when VPS is ready:
```
POST https://n8n.systack.net/webhook/saas-vps-ready
{
  "client_id": "CLIENT001",
  "vps_ip": "123.45.67.89",
  "tailscale_ip": "100.x.x.x",
  "status": "ready",
  "timestamp": "2026-06-17T06:18:00Z"
}
```

### With Dashboard
Provisioning status written to:
- `/tmp/saos-deployment-{client_id}.json` (local)
- `saos_deployments` table in Postgres (via n8n webhook)

---

## What's Missing / Next Steps

| Item | Status | Notes |
|------|--------|-------|
| Vultr API key | ❌ Needed | Get from Vultr dashboard → API → Add key |
| Tailscale auth key | ❌ Needed | Generate in Tailscale admin → Keys |
| OpenClaw install URL | ⚠️ Placeholder | Currently uses get.openclaw.ai (verify) |
| n8n webhook endpoint | ⚠️ Need to create | `saas-vps-ready` webhook in n8n |
| Real VPS test | ⏳ Blocked | Waiting for API keys |
| Identity file deployment | ⏳ Next step | Generate + SCP to VPS after creation |
| Health check validation | ⏳ Next step | VALI-style checks after provision |

---

## Files Committed

| File | Action |
|------|--------|
| `scripts/provision_vps.py` | NEW |
| `scripts/test_provision.py` | NEW |

**Commit:** `40cb7dc` — "Add Vultr VPS provisioning script with tests"
**Repo:** https://github.com/Phillip-Lowe/systack-saas.git

---

## Credential Requirements

### Vultr API Key
1. Login to https://my.vultr.com/
2. Go to Account → API → Add API Key
3. Copy key → store securely (keychain: `vultr-api-key`)
4. Scope needed: `compute:write`, `compute:read`

### Tailscale Auth Key
1. Login to https://login.tailscale.com/admin
2. Settings → Keys → Generate auth key
3. Tag: `tag:saos-client`
4. Reusable: Yes (for automation)
5. Ephemeral: No
6. Store securely (keychain: `tailscale-auth-key`)

---

*Session saved 2026-06-17 06:18 CDT*
*Next: Get API keys and run first real VPS test*
