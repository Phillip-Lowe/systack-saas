# ORACLE EXECUTION CURRICULUM (4-WEEK SYSTEM)

**Version:** Resource-Aware Comparative Execution
**Created:** 2026-06-20
**Owner:** ORACLE
**Status:** ACTIVE
**Previous:** 2-Week Critical Infrastructure (deprecated — too basic)

---

## CORE PRINCIPLES

**1. Every task must be:**
- **Novel** — Not something the fleet already knows
- **Comparative** — Research 2-3 approaches, evaluate, implement best
- **System-relevant** — Solves real SAOS/Systack gap
- **Artifact-producing** — Output must be executable/testable/deployable

**2. Resource constraints (HARD):**
- **MacBook Air M1 — 8GB unified RAM**
- **154GB disk used / 35GB free** — tight but workable
- **Ollama models available:** `qwen2.5-coder:7b` (4.7GB), `nomic-embed-text` (274MB)
- **Heavy models** (qwen3.5:9b at 6.6GB) — only one at a time, close others first
- **ComfyUI video generation** — **NOT viable on 8GB RAM.** Use cloud (Kling/Runway) or research only
- **ComfyUI image generation** — Verify if still works; if not, use cloud or skip

**3. Model scheduling by task type:**

| Task Type | Model | VRAM | When |
|-----------|-------|------|------|
| Light research (JURIS, CHATTY) | None needed — web_search + read/write | 0GB | Anytime |
| Build scripts (ASSEMBLY, SOL) | `qwen2.5-coder:7b` | ~5GB | Anytime |
| Code generation (CODY) | `qwen2.5-coder:7b` | ~5GB | Anytime |
| Architecture review (ORACLE) | `qwen2.5-coder:7b` | ~5GB | Anytime |
| Heavy reasoning (PESSI, VALI) | `qwen3.5:9b` | ~7GB | When Ollama idle |
| Image/Video (GENI) | **Cloud only** (Kling/Runway) | 0GB local | Scheduled separately |

**4. NOT allowed:**
- Generic tutorials, theory-only research
- Anything marked "Already Know" in system memory
- Local video generation (resource impossible)
- Running multiple heavy models simultaneously

---

## FLEET STATE SNAPSHOT

| Capability | Status | Gap |
|-----------|--------|-----|
| VPS provisioning scripts | ✅ Built | ✅ API keys obtained — ready to test |
| Tailscale tagged devices | ✅ Configured | ✅ API keys obtained — ready to test |
| n8n workflow import | ✅ Working | ✅ API key obtained — ready to expand |
| Ollama local models | ✅ Running | ❌ qwen2.5-coder only (4.7GB), 8GB RAM limit |
| ComfyUI image gen | ⏳ Unknown | ❌ Probably broken, 8GB too small for video |
| Client dashboard | ✅ Built | ❌ No auth system |
| Stripe catalog | ✅ Created | ❌ No webhook integration |
| SAOS agent fleet | ✅ 10 agents | ❌ No automated compliance gate |
| Utopia Deli systems | ✅ Active | ❌ No regression testing |
| Content pipeline (MOD 1) | ⏳ Partial | ❌ No copyright/license automation |
| Knowledge management | ✅ Wiki + RAG | ❌ No fleet knowledge graph |

**Key change:** API keys are NOW AVAILABLE. Tasks should use real credentials, not mock data.

---

## 4-WEEK SCHEDULE

### WEEK 1 — REAL DEPLOYMENT PIPELINE (Keys Available)

#### MONDAY — SOL
**Topic:** Auto-Provisioning End-to-End with Real API Keys
**Gap:** Scripts built, never tested with real Vultr/Tailscale
**Task:** Use real credentials to create test VPS (smallest tier, $5/mo, destroy after). Test full pipeline: VPS → Tailscale tag → n8n webhook → health check. Document: API behavior, timing, failure modes, cost.
**Artifact:** Working end-to-end provision with real APIs + timing data + failure documentation.
**Comparative:** Vultr API v2 vs direct SSH vs cloud-init — which is fastest/most reliable?
**Model:** `qwen2.5-coder:7b` (script execution)
**Resource note:** Close Ollama after if heavy tasks follow.

#### TUESDAY — CODY
**Topic:** SAOS Client Bootstrap Generator
**Gap:** Every new client needs manual file creation, identity setup, config generation
**Task:** Research 2 approaches: (1) Cookiecutter-style template engine, (2) AI-generated bootstrap with validation. Build system: input client name + tier + business type → generates full workspace: AGENTS.md, SOUL.md, IDENTITY.md, MEMORY.md, TOOLS.md, skills/ structure. Test with 2 fake clients.
**Artifact:** Bootstrap generator + 2 sample client workspaces + customization guide.
**Comparative:** Template engine vs AI generation — consistency, speed, customization.
**Model:** `qwen2.5-coder:7b`

#### WEDNESDAY — PESSI
**Topic:** Chaos Engineering for SAOS
**Gap:** Never test failure recovery
**Task:** Research 2 approaches: (1) Custom script chaos (kill processes, drop network), (2) Provider API chaos (Vultr: reboot VPS mid-deploy). Implement 3 scenarios: VPS dies mid-deploy, n8n webhook timeout, Tailscale disconnect. Test recovery procedures with real infrastructure.
**Artifact:** 3 chaos tests + recovery runbooks + real failure data.
**Comparative:** Custom chaos vs provider APIs vs Gremlin (cost).
**Model:** `qwen2.5-coder:7b` (scripts) or `qwen3.5:9b` (architecture reasoning)

#### THURSDAY — CHATTY
**Topic:** Client Self-Service Onboarding Form
**Gap:** Manual discovery → days to deploy
**Task:** Research 2 approaches: (1) Custom HTML form + webhook → auto-provision, (2) Tally.so form + n8n integration. Build form that captures: business info, data classification (Tier 1-4), menu/products, payment pref, auth pref. Output: client config JSON that feeds ASSEMBLY deployment.
**Artifact:** Working onboarding form + config generator + test with fake data.
**Comparative:** Custom HTML vs Tally.so — dev time, customization, data handling.
**Model:** None needed (HTML/webhook work)

#### FRIDAY — GENI
**Topic:** Video Generation Strategy (Resource-Constrained)
**Gap:** Image gen unclear, video gen = zero
**Task:** **DO NOT attempt local video on 8GB RAM.** Research 3 cloud options: (1) Kling AI (lifetime sub, Apple auth), (2) Runway ML (855 credits, team: loudgreen1), (3) n8n + API integration. Build decision matrix: cost/credit, quality, speed, API availability. Test ONE option with existing account. Output: working video + integration guide.
**Artifact:** Cloud video generation comparison + 1 working sample + n8n workflow template.
**Comparative:** Kling vs Runway vs API — for SAOS marketing content.
**Model:** None needed (research + API calls)
**Resource note:** ZERO local VRAM usage. Use existing cloud subscriptions.

#### SATURDAY — VALI
**Topic:** n8n Workflow Regression Test Suite
**Gap:** Workflows break silently
**Task:** Research 2 approaches: (1) Webhook replay with stored payloads, (2) n8n execution API + validation. Build test harness for Utopia Deli catering workflow: test webhook → verify SQLite write → check email trigger. Use REAL n8n API key to execute tests.
**Artifact:** Test harness + 3 test cases + pass/fail report + n8n API integration.
**Comparative:** Webhook replay vs execution API — coverage, speed, maintenance.
**Model:** `qwen2.5-coder:7b` (scripting)

#### SUNDAY — JURIS
**Topic:** Automated Compliance Gate
**Gap:** Manual review only, no pre-deploy scan
**Task:** Research 2 approaches: (1) Custom config parser + rule engine, (2) Semgrep + custom rules for SAOS. Build scanner that reads deployment configs, flags: missing data classification, hardcoded credentials (check against credentials/ folder), open ports, no rollback plan, no backup config. Test against real provisioning scripts.
**Artifact:** Compliance scanner + rule set + integration with deployment gate.
**Comparative:** Custom parser vs Semgrep — expressiveness, performance, maintenance.
**Model:** `qwen2.5-coder:7b` or none (parsing work)

---

### WEEK 2 — CLIENT SYSTEMS + AUTH

#### MONDAY — SOL
**Topic:** Stripe Webhook Integration
**Gap:** Catalog exists, no checkout automation
**Task:** Use REAL Stripe test keys (from credentials/Green/stripe/). Implement webhook: Stripe checkout → n8n → client provisioning trigger. Test with Stripe CLI. Handle: idempotency, retries, failed payments, subscription events.
**Artifact:** Working webhook endpoint + n8n workflow + test suite + error handling.
**Comparative:** Direct n8n vs queue-based vs event bus.
**Model:** `qwen2.5-coder:7b`

#### TUESDAY — CODY
**Topic:** SAOS Skill Architecture Redesign
**Gap:** Skills scattered, no unified structure, hard to maintain
**Task:** Research 2 approaches: (1) Modular skill system with shared libs, (2) Monolithic skill with conditional execution. Design skill architecture that: loads per-agent, shares common utilities, version-controlled, auto-updates. Build prototype with 3 skills.
**Artifact:** Skill architecture spec + prototype + 3 working skills + migration guide.
**Comparative:** Modular vs monolithic — maintenance, load time, dependency hell.
**Model:** `qwen2.5-coder:7b` or `qwen3.5:9b`

#### WEDNESDAY — ASSEMBLY
**Topic:** Zero-Downtime SAOS Client Deployment
**Gap:** Deploy = n8n import, no rollback strategy
**Task:** Design deployment with 2 real approaches: (1) Blue/green VPS swap using Vultr API, (2) n8n workflow versioning with instant rollback. Test rollback in 2 minutes. Document: cost of blue/green vs complexity.
**Artifact:** Deployment orchestrator with strategy selector + 2 tested approaches + rollback demo.
**Comparative:** Blue/green vs rolling vs canary — for SAOS specifically.
**Model:** `qwen2.5-coder:7b`

#### THURSDAY — CHATTY
**Topic:** Automated Client Communication Pipeline
**Gap:** Manual emails, no systematic updates
**Task:** Research 2 approaches: (1) n8n email sequences, (2) Resend API (check if key available). Build pipeline: signup → welcome sequence → deployment updates → monthly health report. Test with fake journey.
**Artifact:** Communication workflow + templates + scheduling + test journey.
**Comparative:** n8n sequences vs Resend — deliverability, analytics.
**Model:** None needed

#### FRIDAY — GENI
**Topic:** SAAS Marketing Asset Generator (Cloud-Only)
**Gap:** Manual visuals per client
**Task:** Use cloud tools: (1) Kling/Runway for video, (2) Canva API or DALL-E for images. Build system: input brand colors + business type → output logo variant + hero image + social pack. Test with 2 fake brands. **NO local generation.**
**Artifact:** Asset generator workflow + 2 sample brand packs + cloud integration guide.
**Comparative:** Kling/Runway video + DALL-E/Canva images — cost, quality, brand consistency.
**Model:** None needed
**Resource note:** All cloud. Zero local VRAM.

#### SATURDAY — ATLAS
**Topic:** Fleet Knowledge Graph
**Gap:** Wiki + RAG but no structured agent capability map
**Task:** Research 2 approaches: (1) Structured markdown + wikilinks (Obsidian-style), (2) Graph database (Neo4j/memgraph). Build queryable map: agent → capabilities → skills → tools → outputs. Test queries: "Which agent handles video?" → GENI → Kling/Runway → n8n workflow.
**Artifact:** Knowledge graph schema + populated data + 5 test queries + query interface.
**Comparative:** Markdown graph vs graph DB — query speed, maintenance, portability.
**Model:** None needed (organizational work)

#### SUNDAY — JURIS
**Topic:** Client Contract Auto-Generator
**Gap:** Manual contract drafting
**Task:** Build hybrid system: templates (Bronze-Platinum) + AI fill for custom terms. Input: client tier + data classification + special terms. Output: contract + ToS + DPA. JURIS reviews AI output for risks.
**Artifact:** Contract generator + 5 tier templates + JURIS review integration.
**Comparative:** Template engine vs AI gen vs hybrid.
**Model:** `qwen2.5-coder:7b` or none

---

### WEEK 3 — SCALE + INTELLIGENCE

#### MONDAY — SOL
**Topic:** Multi-Agent Orchestrator Upgrade
**Gap:** SOL spawns manually, no dynamic task routing
**Task:** Research 2 approaches: (1) Static round-robin with priority override, (2) Capability-based routing with load balancing. Implement: task arrives → analyze required skills → route to best available agent → track completion → retry on failure. Test with 10 simulated tasks.
**Artifact:** Dynamic orchestrator + agent capability registry + task router + retry logic.
**Comparative:** Round-robin vs capability routing — throughput, fairness, accuracy.
**Model:** `qwen2.5-coder:7b` or `qwen3.5:9b`

#### TUESDAY — CODY
**Topic:** n8n Node Development
**Gap:** Custom n8n nodes = none, limited to built-in
**Task:** Research 2 approaches: (1) Custom n8n node (TypeScript), (2) n8n Code node with external libs. Build custom node for SAOS-specific operations: agent spawn, compliance check, deployment trigger. Test in local n8n.
**Artifact:** Custom n8n node + package + documentation + test workflow.
**Comparative:** Custom node vs Code node — maintainability, versioning, distribution.
**Model:** `qwen2.5-coder:7b`

#### WEDNESDAY — PESSI
**Topic:** Predictive Failure Detection
**Gap:** Reactive monitoring, no prediction
**Task:** Research 2 approaches: (1) Threshold-based anomaly detection, (2) Log pattern matching. Implement: monitor n8n execution logs → detect patterns before failure → alert 5 minutes early. Test with induced failures.
**Artifact:** Predictive monitor + detection method + alert system + accuracy report.
**Comparative:** Threshold vs pattern matching — accuracy, false positive rate, compute cost.
**Model:** `qwen2.5-coder:7b` or `qwen3.5:9b`

#### THURSDAY — CHATTY
**Topic:** Multi-Channel Client Support Bot
**Gap:** Support scattered across channels
**Task:** Research 2 approaches: (1) n8n multi-channel hub, (2) Unified inbox API. Build bot that reads: Signal + email + webchat → classifies urgency → routes to appropriate agent → drafts response → CHATTY reviews → sends. Test with 3 mock tickets.
**Artifact:** Support hub + classifier + routing + draft generator + 3 test tickets.
**Comparative:** n8n hub vs unified inbox vs custom router — latency, context preservation, cost.
**Model:** None needed

#### FRIDAY — GENI
**Topic:** Dynamic Brand System (Cloud-Assisted)
**Gap:** Static templates, no adaptive branding per client
**Task:** Build runtime theme engine: input brand description → generates palette + typography → applies to all touchpoints. Use cloud APIs for palette generation if needed.
**Artifact:** Dynamic brand engine + 3 sample systems + integration guide.
**Comparative:** CSS vars vs theme engine vs AI palette.
**Model:** None needed (mostly CSS/dev work)

#### SATURDAY — ATLAS
**Topic:** Cross-Agent Memory Integration
**Gap:** Each agent has own memory, no shared context
**Task:** Research 2 approaches: (1) Shared wiki with agent-specific views, (2) Centralized knowledge base with agent filters. Build system: agent writes to shared store → other agents can query relevant context → maintains privacy boundaries. Test with SOL + CODY handoff.
**Artifact:** Shared memory system + privacy model + SOL-CODY test handoff + docs.
**Comparative:** Shared wiki vs centralized KB — privacy, latency, consistency.
**Model:** None needed (knowledge architecture)

#### SUNDAY — JURIS
**Topic:** Regulatory Update Monitor
**Gap:** Compliance docs go stale
**Task:** Research 2 approaches: (1) RSS/feed monitoring, (2) Web scraping + diff. Build monitor: tracks GDPR/CCPA/state law changes → summarizes impact → flags updates → proposes doc changes. Test with recent CA privacy law.
**Artifact:** Regulatory monitor + 3 sources + summarizer + sample update proposal.
**Comparative:** RSS vs scraping — coverage, latency, accuracy, maintenance.
**Model:** None needed (web monitoring)

---

### WEEK 4 — OPTIMIZATION + NOVEL CAPABILITIES

#### MONDAY — SOL
**Topic:** SAOS Self-Healing System
**Gap:** Manual intervention when things break
**Task:** Research 2 approaches: (1) Restart + retry with backoff, (2) Fallback agent with degraded mode. Implement: detect n8n failure → auto-restart → fallback agent → degraded mode (notify human). Test with 3 failure types.
**Artifact:** Self-healing controller + 3 recovery strategies + failure simulator + incident log.
**Comparative:** Restart vs fallback vs degraded — recovery time, user impact, complexity.
**Model:** `qwen2.5-coder:7b`

#### TUESDAY — CODY
**Topic:** Automated Skill Testing Framework
**Gap:** Skills untested, break silently when tools change
**Task:** Research 2 approaches: (1) Unit tests per skill function, (2) Integration tests with real tool calls. Build framework: tests skill loading → validates tool calls → checks output format → regression on tool updates. Test with 2 existing skills.
**Artifact:** Skill test framework + 2 tested skills + CI-style report + docs.
**Comparative:** Unit vs integration tests — coverage, speed, flakiness, maintenance.
**Model:** `qwen2.5-coder:7b`

#### WEDNESDAY — ASSEMBLY
**Topic:** Edge Deployment (Client On-Premise)
**Gap:** All deployments are cloud VPS
**Task:** Research 2 approaches: (1) Docker Compose on Pi 4/5, (2) Kubernetes k3s. Build on-premise package: auto-updates via Tailscale, offline-first, syncs when connected. Test on local VM.
**Artifact:** Edge deployment package + install script + auto-update + offline test.
**Comparative:** Docker Compose vs k3s — resource use, reliability, update ease.
**Model:** `qwen2.5-coder:7b`

#### THURSDAY — CHATTY
**Topic:** Client Success Automation
**Gap:** No systematic health/engagement checks
**Task:** Research 2 approaches: (1) Usage-based health scoring, (2) Proactive outreach triggers. Monitor engagement (dashboard logins, n8n executions, tickets) → health score → declining → outreach. Test with 2 fake profiles.
**Artifact:** Health scoring + intervention trigger + outreach templates + 2 test profiles.
**Comparative:** Usage scoring vs proactive — response rate, actionability, privacy.
**Model:** None needed

#### FRIDAY — GENI
**Topic:** AI-Generated Documentation
**Gap:** Manual docs drift from code
**Task:** Research 2 approaches: (1) AI doc generators from code, (2) Screenshot automation + visual diffs. Build doc generator: reads n8n JSON → human-readable doc + screenshots → updates on workflow change.
**Artifact:** Doc generator + 1 workflow doc + auto-update hook.
**Comparative:** AI gen vs screenshot — accuracy, maintenance, user preference.
**Model:** None needed (mostly parsing work)

#### SATURDAY — ATLAS
**Topic:** Agent Performance Benchmarking
**Gap:** No metrics on which agents are effective
**Task:** Research 2 approaches: (1) Task completion rate tracking, (2) Quality scoring with human review. Track tasks → score completion time, quality, retry rate → weekly report → flag underperformers. Use existing memory/learning/ files.
**Artifact:** Benchmark system + scoring rubric + weekly report generator + dashboard.
**Comparative:** Completion rate vs quality score — bias, granularity, actionability.
**Model:** `qwen2.5-coder:7b`

#### SUNDAY — JURIS
**Topic:** Automated Incident Response
**Gap:** Manual breach response
**Task:** Research 2 approaches: (1) Runbook automation with checklists, (2) SOAR-style automated playbook. Build: detect incident → classify severity → execute response → generate report → update compliance docs. Test with simulated breach.
**Artifact:** Incident response automation + severity classifier + playbook + test simulation.
**Comparative:** Runbook vs SOAR — speed, coverage, cost, audit trail.
**Model:** `qwen2.5-coder:7b` or none

---

## RESOURCE MANAGEMENT RULES

### Model Loading Schedule
```
Before heavy task (qwen3.5:9b):
1. Check current Ollama models: curl http://localhost:11434/api/tags
2. If other models loaded, unload them first
3. Load target model
4. Execute task
5. Unload after completion (optional — keep if next task needs it)
```

### GENI Special Rule
```
GENI tasks NEVER load local video/image models.
Options:
- Use Kling AI (lifetime sub, check Apple auth)
- Use Runway ML (855 credits, team: loudgreen1)
- Use Canva API or DALL-E if API key available
- If no cloud available: research-only task (comparison matrix)
```

### Disk Space Warning
```
Current: 154GB used / 35GB free (82% full)
Tasks should:
- Clean up test artifacts after validation
- Use external storage for large outputs (iCloud, not local)
- Monitor before operations that create large files
```

---

## GAP STATE TRACKING

```
UNTOUCHED → IN PROGRESS → PARTIAL → OPERATIONAL → SYSTEMIZED
```

Update at end of each session. JURIS verifies SYSTEMIZED items have compliance check.

---

## NON-NEGOTIABLE RULES

1. **NO TUTORIALS** — Build or research with implementation intent
2. **NO REPEATS** — Skip if "Already Know" in memory
3. **COMPARATIVE REQUIRED** — Every task starts with "Research 2-3 approaches..."
4. **ARTIFACT OR NOTHING** — Output must be executable/testable/deployable
5. **USE REAL KEYS** — API keys available in credentials/ folder. No mock data.
6. **RESOURCE CHECK** — Before loading models, verify available RAM/disk
7. **CLEAN UP** — Test artifacts, temp files deleted after validation
8. **JURIS REVIEWS ALL** — Before SYSTEMIZED, compliance check
9. **VALI VALIDATES** — Before SYSTEMIZED, test the artifact
10. **PESSI STRESS-TESTS** — Before SYSTEMIZED, find failure modes

---

## FLEET ROTATION (10 AGENTS)

| Day | Agent | Focus | Model | Resource |
|-----|-------|-------|-------|----------|
| Monday | SOL | Orchestration / Pipeline | `qwen2.5-coder:7b` | ~5GB |
| Tuesday | CODY | Build Patterns / Skill Architecture | `qwen2.5-coder:7b` | ~5GB |
| Wednesday | ATLAS | Knowledge / RAG | `qwen2.5-coder:7b` or none | ~5GB |
| Thursday | PESSI | Risk / Chaos / Prediction (bi-weekly) | `qwen2.5-coder:7b` or `qwen3.5:9b` | ~5-7GB |
| Friday | CHATTY | Communication / Onboarding (bi-weekly) | None | 0GB |
| Saturday | GENI | Creative / Generation (bi-weekly) | None (cloud) | 0GB |
| Sunday | VALI | Validation / Testing (bi-weekly) | `qwen2.5-coder:7b` | ~5GB |

**Bi-weekly pattern:**
- **Week 1 (odd):** PESSI Thu, CHATTY Fri, GENI Sat, VALI Sun
- **Week 2 (even):** ASSEMBLY Thu, JURIS Fri, *(off Sat)*, *(off Sun)*

**Weekly agents (every week):** SOL, CODY, ATLAS

**On-Demand Only:**
- **ORACLE 🔮** — Curriculum design, architecture. Any agent can reach out via `sessions_send` or browser automation for insights/direction.

**Agent interaction model:** Any agent can reach out to any other agent. Common patterns:
- JURIS → ORACLE: "Review this curriculum for compliance gaps"
- SOL → ORACLE: "System needs architecture redesign"
- CODY → ORACLE: "Need design pattern for [X]"
- ASSEMBLY → CODY: "How do I implement this design?"
- any → JURIS: "Review before deploy"

---

*Updated: 2026-06-20 02:04 CDT*
*Replaces: June 8 rotation + June 20 initial draft*
*Key changes: SOL/CODY/ATLAS weekly Mon-Wed, all others bi-weekly Thu-Sun, resource-aware scheduling, real API keys, all agents can interact with ORACLE*
