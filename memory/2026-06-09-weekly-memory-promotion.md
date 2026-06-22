# Weekly Manual Memory Promotion Report

**Date:** Tuesday, June 9, 2026 — 9:02 AM CDT  
**Promoter:** Sol (Manual Curation)  
**Reason:** Dreaming system broken (hardcoded thresholds unreachable with nomic-embed-text)

---

## Summary

| Metric | Value |
|--------|-------|
| Daily logs scanned | 100+ files (June 2–June 9) |
| Dreaming backfill entries checked | 30 entries (April 26–May 25) |
| Dreaming light/deep directories checked | 8 files each (June 2–June 9) |
| New sections promoted to MEMORY.md | 11 |
| Lines added to MEMORY.md | ~266 (1542 → 1808) |
| Duplicate/redundant entries | 0 (all verified not already in MEMORY.md) |

---

## What Was Promoted (11 Sections)

### 1. 2026-06-09 — ORCHESTRATOR SYSTEM BUILT
**Source:** `memory/2026-06-09-orchestrator-complete.md`, `memory/2026-06-09-oracle-orchestration-spec.md`
**Why:** Complete system rebuild — replaces ALL broken cron jobs with Postgres + Python orchestration. Fundamental architecture change.
**Key facts:**
- 4 files created (orchestrator.py, planner.py, openclaw_bridge.py, daily_learning_orchestrator.py)
- 4 Postgres tables (task_queue, agent_state, execution_log, message_bus)
- 7 agents seeded
- 4 tasks completed, 0 failures
- Daily learning fix: qwen2.5-coder:7b + 900s timeout

### 2. 2026-06-09 — LINKEDIN POST 2 PUBLISHED
**Source:** `memory/2026-06-09-linkedin-post2-posted.md`
**Why:** Social media presence building, content series tracking
**Key facts:**
- Posted ~8:19 AM CDT
- Build journey / career pivot theme
- 5 hashtags, global visibility
- URL: https://www.linkedin.com/feed/update/urn:li:activity:7470099331203678208/

### 3. 2026-06-09 — RAG SYSTEM DEPLOYED
**Source:** `memory/2026-06-09-rag-system-deployed.md`
**Why:** Infrastructure milestone — local RAG for knowledge retrieval
**Key facts:**
- pgvector + Ollama (qwen2.5-coder:7b + nomic-embed-text)
- Tested with invoice knowledge queries
- Next: Add Systack docs, n8n workflows, MEMORY.md as sources

### 4. 2026-06-08 — ORACLE RSI SYSTEM REBUILD + VALIDATION ENVIRONMENT
**Source:** `memory/2026-06-08-evening-session-save.md`
**Why:** Major system overhaul — removed broken infrastructure, replaced with working system
**Key facts:**
- Removed: 10 failed cron jobs, CODY builds, ERROR-WATCHDOG
- Created: ORACLE-CURRICULUM.md, VALIDATION-ENVIRONMENT-POLICY.md, AGENT-ROTATION-SCHEDULE.md
- Resource savings: ~85% fewer spawns, zero delivery errors
- 6 active cron jobs (down from 10+)

### 5. 2026-06-08 — INVOICE EMAIL PIPELINE FULLY OPERATIONAL
**Source:** `memory/2026-06-08-invoice-pipeline-complete.md`
**Why:** Revenue-critical system — proven working end-to-end
**Key facts:**
- Workflow ID: Ny4kzzf1bN4NODGn
- Execution #439 proof: real invoice extracted and saved
- 4 technical fixes documented (binary data, multipart, IPv4, published version)
- 3 monetization options defined

### 6. 2026-06-08 — CATERING LEAD SYSTEM V2.1 COMPLETE
**Source:** `memory/2026-06-08-catering-deployment-complete.md`
**Why:** Major client deliverable — complete system with scoring + emails
**Key facts:**
- 7-factor scoring engine (0-100)
- 3-tier response (ACCEPT/REVIEW/REJECT)
- 6 key fixes documented (API key, JS syntax, regex, routing, webhook path, EmailSend)
- Payment policy: 50% deposit, balance 2 weeks prior

### 7. 2026-06-08 — POSTGRES PRIMARY DATABASE DECISION
**Source:** `memory/2026-06-08-postgres-cleanup.md`, `memory/2026-06-08-hybrid-memory-system-complete.md`
**Why:** Infrastructure decision affecting all future development
**Key facts:**
- Postgres over SQLite for new data
- pgAdmin 4 v9.15 installed
- Hybrid memory system: systack_memory DB, 538 sources, 8 entities
- Files: memory_sync.py, memory_query.py, memory_schema.sql

### 8. 2026-06-08 — LESSON: CHECK CREDENTIALS BEFORE SAYING "I DON'T KNOW"
**Source:** `memory/2026-06-08-lessons-credentials.md`
**Why:** Behavioral rule — prevents repeated user frustration
**Key facts:**
- 5-step credential check pattern
- 4 credentials found during session
- Added to MEMORY.md as operating rule

### 9. 2026-06-07 — STRIPE BUY BUTTONS BROKEN → DIRECT LINKS
**Source:** `memory/2026-06-07-stripe-buttons-broken.md`
**Why:** Revenue-critical fix — customers couldn't sign up
**Key facts:**
- Embedded Buy Buttons fail despite correct configuration
- Direct Stripe Checkout links work
- Pricing page updated
- Buy Buttons disabled pending Stripe fix

### 10. 2026-06-07 — INVOICE PARSER: 9 FORMATS + OCR
**Source:** `memory/2026-06-07-invoice-parser-bulletproof.md`
**Why:** Core product capability — parser robustness
**Key facts:**
- 9 formats including OCR fallback
- Real PDFs tested (AT&T bill, scanned documents)
- API server + Cloudflare tunnel deployed
- Blocker: Gmail app password revoked

### 11. DREAMS.md Backfill Entries (April–May 2026)
**Source:** `DREAMS.md` (30 backfill entries)
**Why:** Ensure historical continuity
**Key facts:**
- April 26: Work Slack channel configuration
- May 2: Obsidian setup, monetization plan, 28 n8n workflows mapped
- May 3: SDXL preference, disk space (28GB free)
- May 4: ComfyUI setup, TTS config
- May 6: "Remember this" protocol established
- May 7: Daily 9 AM briefing request
- May 8: Agent cognition schema (max 5 steps)
- May 9: SITESCHEMA.md created
- May 10: Personal struggles, native tools preference
- May 11: Music catalog logged, "remember this" reinforced
- May 12: Caddy proxy, Plan & Goal Protocol, Obsidian iCloud sync
- May 13: Tremell Billings referral
- May 14: n8n workflow analysis, domain architecture (CNAME not nameserver)
- May 16: Google Sheets sync, capability audit
- May 17: Tunnel routing fixed, auto-start LaunchAgents, Square payment link
- May 18: Gateway crash, career roadmap ($45K-65K target)
- May 19: Session recovery patches, registry empty fix
- May 20: Named tunnel, disabled broken workflows
- May 21: Logo tweaks, BlueBubbles routing fixed
- May 23: Drift linter, AGENTS.md enforcement layer
- May 25: GitHub Pages, brand config separation, integration check

---

## What Was NOT Promoted (And Why)

### Watchdog Reports (June 6–7)
- ERROR-WATCHDOG was itself broken
- Reports are operational noise, not durable memory
- CODY dormant since May 31 — no build activity to track

### OpenClaw Release Notes (June 2–9)
- Already captured in `memory/2026-06-0X-openclaw-releases.md` files
- Dreaming captured some, but thresholds prevented promotion
- Key fixes (MCP coercion, QQBot stripping) are in release files

### Multiple Session Saves (Duplicate Content)
- `2026-06-08-session-summary.md` — overlaps with pipeline + morning session
- `2026-06-08-catering-lead-system.md` — overlaps with v2.1 deployment
- `2026-06-06-utopia-deli-modifiers.md` — implementation detail, not architecture

### Pricing Discussions (June 6)
- `2026-06-06-final-pricing-decision.md` — $199 minimum (already in MEMORY.md from June 6 backfill)
- `2026-06-06-pricing-alignment.md` — Percy = SAOS Personal+ (already documented)

### Session Save Files (June 5)
- `2026-06-05-session-save.md` — captured in MEMORY.md "2026-06-05 — DREAMING SYSTEM BROKEN"
- `2026-06-05-context-as-infrastructure.md` — already in MEMORY.md
- `2026-06-05-context-window-assembly.md` — prompting evolution (already in MEMORY.md)

---

## Verification

- [x] MEMORY.md re-read to check for duplicates
- [x] All promoted sections start with "## 2026-06-0X" for chronological sorting
- [x] Source files cited for traceability
- [x] No contradictions with existing MEMORY.md content
- [x] Technical details preserved (workflow IDs, table names, file paths)

---

## Promotion Method

**Manual curation — NO dreaming involved**
1. Listed all `memory/2026-*.md` files
2. Read candidate files (focusing on non-watchdog, non-release)
3. Checked DREAMS.md for backfill entries (April–May 2026)
4. Verified not already in MEMORY.md via search
5. Wrote directly to MEMORY.md in proper sections
6. Added to bottom for chronological ordering

**Why manual:** Dreaming thresholds (minScore=0.8) unreachable with nomic-embed-text (scores 0.43-0.52). OpenClaw Issue #65402 confirmed hardcoded.

---

## Next Promotion

**Scheduled:** Tuesday, June 16, 2026 — 9:00 AM CDT (weekly cron)
**Expected candidates:**
- Daily agent learning outputs (memory/learning/)
- Invoice pipeline dashboard (if built)
- Catering system real-world testing results
- Stripe button fix (if resolved)
- n8n credential management (ASSEMBLY Week 1 topic)

---

*Report generated: 2026-06-09 14:02 UTC*  
*Status: Complete — 11 sections promoted, 266 lines added*
