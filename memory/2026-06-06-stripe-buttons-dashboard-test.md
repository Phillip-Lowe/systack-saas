# 2026-06-06 — Stripe Buttons + Dashboard Test

## Dashboard Test Results

### ✅ Dashboard Server Started Successfully
- Flask server running on `http://127.0.0.1:8080`
- Auto-detected n8n database at `~/.n8n/database.sqlite`
- Created `~/.systack/dashboard.sqlite` for dashboard state

### ✅ Health Check
```json
{
    "disk": {"free_gb": 15.35, "status": "ok"},
    "n8n": {"status": "ok", "last_check": "2026-06-06T18:56:00"},
    "ollama": {"status": "ok", "last_check": "2026-06-06T18:56:00"},
    "postgres": {"status": "unknown"}
}
```

### ✅ Metrics
```json
{
    "today_count": 10,
    "pending_count": 0,
    "alert_count": 0,
    "success_rate": 80.0
}
```

- Today count pulled from dashboard DB (10 existing entries)
- Success rate calculated from completed vs total (80%)
- Postgres shows "unknown" because no Postgres connection configured yet

### ⚠️ Note
- Server is running in development mode (not production WSGI)
- For production: use gunicorn or uwsgi
- Background polling thread active (30s intervals)

---

## Stripe Payment Links Added

### Products Configured

| Product | Price | Stripe Link | Button ID |
|---------|-------|-------------|-----------|
| **SAOS Enterprise Fleet** | $799/mo | https://buy.stripe.com/14A7sE9Bn2NVaAU2sm87K02 | `buy_btn_1TfU1m1WicviTxiikPepeQO4` |
| **SAOS Business Fleet** | $299/mo | https://buy.stripe.com/6oUdR2eVHfAH5gA9UO87K01 | `buy_btn_1TfU3M1WicviTxiilXTJNolL` |
| **SAOS Solo Agent** | $149/mo | https://buy.stripe.com/eVqbIUcNz607eRa8QK87K00 | `buy_btn_1TfU451WicviTxiig1l8JYjR` |

### Publishable Key
`pk_live_51Tckdx1WicviTxii6uKLsxzQENJqWDNxt8Zqmst9YKBQ4F0KSn7VpuR7PZTGRQXJMv42NwimR1kcIdOxElznzIsM000DBc6pKp`

### Where Added
- `systack-site/services/service-packages.md` — Private tier ($799) and Accelerate tier ($249) buttons
- `systack-site/services/service-packages.md` — New SAOS Fleet section with all 3 tiers
- `systack-site/stripe-products.md` — Quick reference for all links

### Systack Service Tiers vs SAOS Fleet Tiers

| Systack Tier | Price | SAOS Equivalent |
|-------------|-------|----------------|
| Systack Private (on-premise) | $799/mo | SAOS Enterprise Fleet |
| Systack Accelerate (cloud) | $249/mo | SAOS Business Fleet |
| — | — | SAOS Solo Agent ($149/mo) |

**Note:** Systack Private ($799) = SAOS Enterprise ($799). Systack Accelerate ($249) ≈ SAOS Business ($299). Solo Agent is a new lower tier.

---

## Files Changed
- `systack-site/services/service-packages.md` — Added Stripe buttons + SAOS Fleet section
- `systack-site/stripe-products.md` — Quick reference
- `templates/private/dashboard-server.py` — Tested locally
- `templates/private/dashboard.html` — Verified UI renders
- `templates/private/n8n-log-to-dashboard.json` — Created

## Next
- [ ] Activate n8n workflows (both Private + Accelerate)
- [ ] Build P1 service line templates
- [ ] Test end-to-end with real webhook payload
