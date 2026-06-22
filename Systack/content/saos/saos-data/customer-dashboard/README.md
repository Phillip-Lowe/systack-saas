# SAOS Customer Dashboard

Client-facing portal for SAOS (Systack Agent Orchestration System) customers.

## Files

| File | Purpose |
|------|---------|
| `index.html` | Full client dashboard — Overview, Agents, Tasks, Account tabs |
| `api.py` | Flask API serving client-scoped fleet data |

## Quick Start

```bash
# Start the API
python3 api.py --port 8768

# Serve the HTML (any static server works)
python3 -m http.server 8769

# Open in browser
open http://localhost:8769/index.html?client_id=1
```

## API Endpoints

| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| `/api/portal/status` | GET | `?client_id=N` | Fleet overview + client record |
| `/api/portal/tasks` | GET | `?client_id=N` | Task history (last 50) |
| `/api/portal/agents` | GET | — | All agent states |
| `/api/portal/client` | GET | `?client_id=N` | Single client account details |
| `/api/portal/health` | GET | — | Health check |

## Dashboard Tabs

1. **Overview** — Status banner, metrics (pending/running/completed/agents), VPS info, recent activity
2. **Agents** — Full fleet grid with emoji, role, status dot, current task
3. **Tasks** — Full task history table with status pills
4. **Account** — Business name, email, plan, server IP, active agents, support links

## Auth (Current)

Uses `?client_id=` query parameter (integer). In production, this will be replaced with JWT/session-based auth.

## Design

- Dark theme matching Systack brand (`#001a2d` base)
- Accent: `#00a1db` / `#00c5e0` (Systack cyan)
- Responsive grid layout
- Auto-refresh every 30 seconds
- Tab-based navigation with sticky header

## Related

- Internal fleet dashboard: `../dashboard/index.html` (port 8765)
- SAOS site: `../index.html`
- Fleet agent pages: `../fleet/`
