# Session Save — June 8, 2026 Evening

## What Was Saved

### 1. Recursive Self-Improvement System (Complete Rebuild)

**Broken system removed:**
- 10 failed cron jobs (94 consecutive BlueBubbles errors)
- CODY build jobs (CODY dormant since May 31)
- ERROR-WATCHDOG (was itself broken)

**New system created:**
- `memory/ORACLE-CURRICULUM.md` — Execution curriculum with gap analysis
- `memory/VALIDATION-ENVIRONMENT-POLICY.md` — Sandbox-first testing rules
- `memory/AGENT-ROTATION-SCHEDULE.md` — Updated with execution loop
- `memory/learning/` directory created for daily outputs

**Active cron jobs:**
- Daily Agent Learning — Weekly Rotation (`85ec8a79...`): Daily 10 AM CDT
- Weekly Learning Synthesis (`8cabfd96...`): Sunday 12 PM CDT
- OpenClaw Release Monitor: Daily 9 AM CDT
- Memory Dreaming Promotion: Daily 3 AM CDT
- iCloud wiki sync: Hourly
- LinkedIn reminders: One-time scheduled
- Monthly utilization reviews: June 29

### 2. ORACLE Curriculum (Gap-Driven Execution)

**Already mastered (30+ topics):**
- Frontend: HTML forms, JS cart logic, CORS, validation
- Backend: n8n workflows, Square API, SQLite, MCP tools
- Infrastructure: GitHub Pages, Cloudflare tunnel, Ollama, cron
- Automation: Invoice parsing, email workflows, lead scoring
- AI/ML: Kling AI, ElevenLabs, browser automation, Copilot
- Business: LinkedIn, cold email, discovery questionnaire
- Documentation: Memory system, pitfall tracking, skill creation

**Critical gaps (Week 1):**
- SOL: Error alerting system
- ASSEMBLY: n8n credential management
- PESSI: Webhook idempotency
- CHATTY: Client onboarding automation
- GENI: ComfyUI model activation
- VALI: Payment gateway testing

**Scaling gaps (Week 2):**
- SOL: Log aggregation
- ASSEMBLY: Docker deployment
- PESSI: API rate limiting
- CHATTY: A/B testing
- GENI: Backup strategy
- VALI: n8n testing framework

### 3. Validation Environment Policy (V.E.P.)

**Core rule:** NEVER modify production. NEVER deploy untested.

**Sandbox tools:**
- webhook.site for webhook payloads
- httpbin.org for HTTP testing
- Square Sandbox API for payments
- Local SQLite copies for DB testing
- file:// / python http.server for HTML/JS

**What requires testing:**
- New/edited n8n workflows: Sandbox + approval
- Database schema changes: Sandbox + approval
- Website form/JS changes: Local test + optional approval
- New skills: Isolated test + approval
- Config changes: Sandbox + approval

### 4. Tomorrow's Run (Tuesday 10 AM CDT)

**Agent:** ASSEMBLY
**Topic:** n8n Credential Management
**Objective:** Replace placeholder credentials with working ones
**Sandbox:** Test with Square Sandbox + TEST- Google Sheets
**Deliverable:** Working credential abstraction + documented test results

### 5. Files Created/Updated

| File | Status |
|------|--------|
| `memory/ORACLE-CURRICULUM.md` | Created — complete 2-week execution curriculum |
| `memory/VALIDATION-ENVIRONMENT-POLICY.md` | Created — sandbox testing policy |
| `memory/AGENT-ROTATION-SCHEDULE.md` | Updated — execution loop + VEP reference |
| `memory/learning/` | Created — directory for daily outputs |
| Cron job payload | Updated — reads curriculum + VEP before starting |

---

**Session ended:** June 8, 2026 21:20 CDT
**Next action:** Tomorrow 10:00 AM CDT — ASSEMBLY builds n8n credential system
**Status:** ✅ SAVED EVERYWHERE
