# Why n8n + SQLite Has Issues — The Real Answer

**Date:** 2026-06-05
**n8n Version:** 2.20.7-exp.0 (experimental)
**Status:** Known issues, not unique to us

---

## The Problem Is NOT Us

From the n8n community (live posts from 2025-2026):

**Issue #1: SQLite Lock Timeouts**
- **Error:** `Timeout waiting for lock SqliteWriteConnectionMutex to become available`
- **Frequency:** Happens on n8n Cloud (managed), not just self-hosted
- **Cause:** SQLite can only handle ONE write at a time. Multiple concurrent workflows = lock contention.
- **Impact:** UI hangs, workflows fail, executions get stuck "Queued" for 53+ hours

**Issue #2: Data Loss on Docker Updates**
- **Error:** Workflows "jump back in time" to older state after container restart
- **Cause:** SQLite WAL (Write-Ahead Log) not properly flushed before restart
- **Impact:** Lose days/weeks of workflow changes

**Issue #3: Infinite Boot Loops**
- **Error:** Gateway timeout, crashed execution state in SQLite
- **Cause:** Corrupted execution records block startup

---

## Why SQLite Struggles with n8n

| SQLite Limitation | n8n's Usage | Result |
|-------------------|-------------|--------|
| Single writer | Multiple workflows execute simultaneously | Lock contention, timeouts |
| File-based | Docker volumes, network storage | Corruption, data loss |
| No built-in replication | Need backups, HA | Manual exports, risk |
| WAL mode quirks | Frequent reads/writes | WAL files grow unbounded |

**The core issue:** SQLite was designed for apps with low concurrent write activity (phone apps, browsers). n8n is inherently concurrent — multiple workflows, multiple executions, multiple users.

---

## What Everyone Else Does

From the n8n docs and community:

**Production n8n deployments USE POSTGRES.**

n8n officially recommends:
- **SQLite:** Development, testing, single-user
- **Postgres:** Production, multi-user, high volume
- **MySQL:** Alternative to Postgres

The SQLite issues we're hitting are **well-documented, expected behavior** at scale.

---

## Our Specific Problems (2026-06-03)

### 1. Version Architecture Confusion
We learned: n8n executes from `workflow_history` table, NOT `workflow_entity`.
- We edited `workflow_entity` (draft table) via SQLite
- Executions loaded from `workflow_published_version` → `workflow_history`
- Our edits were completely ignored by the execution engine
- **Fix:** Use n8n UI or MCP API — never edit SQLite directly

### 2. Postgres Node Data Pass-Through
- Postgres node returns `{"success": true}` instead of passing through input data
- Even with `RETURNING *`, downstream nodes don't receive original data
- **Fix:** Use Merge nodes or restructure flow (parallel branches)

### 3. HTTP Request + Buffer Parsing
- n8n 2.20.7 HTTP Request node returns gzip-compressed Buffer
- Code node sandbox doesn't have `Buffer.from()` — can't parse
- **Fix:** Add decompression node, or upgrade n8n version

### 4. Cache Not Clearing
- Workflow changes via MCP don't reflect in executions
- Need explicit `publish_workflow` + n8n restart
- GitHub Issue #24418, #25071 — officially acknowledged

---

## Why We're Different from "Everyone Else"

| "Everyone Else" | Us |
|-----------------|----|
| Use n8n UI for workflow edits | We tried programmatic edits (MCP, SQLite) |
| Use Postgres in production | We used SQLite (default, didn't configure) |
| Upgrade n8n regularly | We're on 2.20.7-exp (experimental, likely outdated) |
| Simple workflows | We built complex multi-node flows with custom code |
| Cloud hosting (managed) | Self-hosted with Cloudflare tunnel |

**We're doing advanced things on a fragile foundation.**

---

## The Fix: What We Need to Do

### Immediate (Next Session)

| Action | Why |
|--------|-----|
| **Switch n8n to Postgres** | Eliminates SQLite lock issues, proper concurrent support |
| **Stop editing SQLite directly** | Use UI or MCP only |
| **Upgrade n8n to stable** | 2.20.7-exp has known bugs; latest is 1.86+ or 2.x stable |
| **Document workflow architecture** | Save JSON backups, version control |

### For Lead Capture Specifically

The lead webhook I built doesn't use the database at all:
- **Webhook** → **Code node** → **Google Sheets** → **Email**
- No SQLite writes. No database contention.
- Google Sheets is the "database" — append-only, no locks.

**This should work even with our current SQLite issues.**

---

## The Real Question

Do we want to:

**A. Fix n8n properly** (Postgres, stable version, proper deployment)
- Time: 2-4 hours
- Result: Reliable foundation for all automations

**B. Work around n8n issues** (keep SQLite, simple workflows only)
- Time: Ongoing pain
- Result: Every new workflow risks more lock issues

**C. Use external services for data** (Supabase, Airtable, Sheets)
- Time: 1 hour setup
- Result: n8n just orchestrates, data lives elsewhere

My recommendation: **A + C** — fix n8n foundation AND use external storage for lead data. Then everything works.

---

## What n8n Version Should We Use?

Current: **2.20.7-exp.0** (experimental, Jan 2025?)
Latest stable: **1.86.0+** or **2.24+** (check with `npm info n8n`)

The `-exp` suffix means experimental. That's part of our problem.

---

*Researched by Sol*
*Source: n8n community posts, GitHub issues #22341, #22380, #24418, #25071*
