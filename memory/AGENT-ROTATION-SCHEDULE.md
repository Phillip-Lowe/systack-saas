# Weekly Agent Rotation Schedule

## How It Works
- **One agent per day** — 7 days, all 10 agents get learning time
- **Runs silently** — writes to files only, no messaging
- **30 minutes max** — research (10 min) + build (15 min) + document (5 min)
- **Curriculum = ORACLE** — Every agent reads `memory/ORACLE-CURRICULUM.md` before starting
- **Validation = REQUIRED** — Every agent reads `memory/VALIDATION-ENVIRONMENT-POLICY.md` before production

## Active Fleet (10 Agents)

| ID | Emoji | Role | Tier | Learning Frequency |
|----|-------|------|------|-------------------|
| sol | 🛰️ | Orchestrator | Execution | Weekly |
| cody | 💻 | Build Engine / Design | Execution | Weekly |
| atlas | 📚 | Knowledge | Intelligence | Weekly |
| vali | ✅ | Validation | Quality/Risk | Bi-weekly |
| pessi | ⚠️ | Risk Analysis | Quality/Risk | Bi-weekly |
| chatty | 💬 | Communication | Engagement | Bi-weekly |
| geni | 🎨 | Creative | Engagement | Bi-weekly |
| assembly | 🛠️ | Deployment | Execution | Bi-weekly |
| juris | ⚖️ | Compliance | Compliance | Bi-weekly |
| oracle | 🔮 | Curriculum / Architecture | Intelligence | On-demand |

**Why this frequency:**
- **SOL weekly:** Orchestrator — needs constant pipeline improvement
- **CODY weekly:** Build engine — designs what ASSEMBLY builds, needs latest patterns
- **ATLAS weekly:** Knowledge — continuous learning on structure, RAG, memory systems
- **All others bi-weekly:** PESSI, CHATTY, GENI, VALI, ASSEMBLY, JURIS — enough to stay current without burning resources
- **ORACLE on-demand:** Curriculum design only — triggered when system needs redesign

## Schedule (10 Agents, 7 Days)

| Day | Agent | Focus | Output |
|-----|-------|-------|--------|
| **Monday** | SOL | Orchestration / Pipeline | `memory/learning/YYYY-MM-DD-sol.md` |
| **Tuesday** | CODY | Build Patterns / Skill Architecture | `memory/learning/YYYY-MM-DD-cody.md` |
| **Wednesday** | ATLAS | Knowledge / RAG | `memory/learning/YYYY-MM-DD-atlas.md` |
| **Thursday** | PESSI | Risk / Chaos / Prediction (bi-weekly) | `memory/learning/YYYY-MM-DD-pessi.md` |
| **Friday** | CHATTY | Communication / Onboarding (bi-weekly) | `memory/learning/YYYY-MM-DD-chatty.md` |
| **Saturday** | GENI | Creative / Generation (bi-weekly) | `memory/learning/YYYY-MM-DD-geni.md` |
| **Sunday** | VALI | Validation / Testing (bi-weekly) | `memory/learning/YYYY-MM-DD-vali.md` |

**Weekly agents (every week):** SOL, CODY, ATLAS
**Bi-weekly agents (every other week):** PESSI, CHATTY, GENI, VALI, ASSEMBLY, JURIS

**Odd week schedule (Week 1, 3, 5...):**
| Day | Agent |
|-----|-------|
| Monday | SOL |
| Tuesday | CODY |
| Wednesday | ATLAS |
| Thursday | PESSI |
| Friday | CHATTY |
| Saturday | GENI |
| Sunday | VALI |

**Even week schedule (Week 2, 4, 6...):**
| Day | Agent |
|-----|-------|
| Monday | SOL |
| Tuesday | CODY |
| Wednesday | ATLAS |
| Thursday | ASSEMBLY |
| Friday | JURIS |
| Saturday | *(off)* |
| Sunday | *(off)* |

## Agent Interaction Model

**Any agent can reach out to any other agent via `sessions_send` or browser automation.**

Common patterns:
- **JURIS → ORACLE:** "Review this curriculum for compliance gaps" (browser automation)
- **SOL → ORACLE:** "System needs architecture redesign" (browser automation, documented in orchestration spec)
- **CODY → ORACLE:** "Need design pattern for [X]" (sessions_send)
- **ASSEMBLY → CODY:** "How do I implement this design?" (sessions_send)
- **VALI → PESSI:** "Found edge case, stress-test this" (sessions_send)
- **ATLAS → any:** "Need to capture this knowledge" (on-demand spawn)
- **any → JURIS:** "Review before deploy" (sessions_send)

**ORACLE is accessible to all agents** — not just SOL. Any agent can open browser automation to ORACLE for insights, direction, or architecture consultation.

## Cron Job
- **ID:** `85ec8a79-b646-451c-82bb-5a2d3e7d65f8` (updated payload)
- **Schedule:** Daily 10:00 AM CDT
- **Delivery:** None (silent — writes to files)
- **Model:** Task-dependent (see Resource Management)
- **Timeout:** 1800s (30 minutes)

## Daily Execution Loop (30 Minutes)

```
0-2 min   → Read memory/ORACLE-CURRICULUM.md → check assigned topic
2-5 min   → memory_search for "already know [topic]" → skip if SYSTEMIZED
5-10 min  → Research 2-3 approaches, compare
10-22 min → Build / design / test (produce artifact)
22-27 min → Format output, write to memory/learning/YYYY-MM-DD-<role>.md
27-30 min → Update GAP STATE in ORACLE-CURRICULUM.md
```

**Rules:**
- Must produce artifact (not notes, not theory)
- Must be comparative (research 2-3, pick best, implement)
- Must be novel (not "Already Know" per memory search)
- Must use real credentials from `credentials/` folder (not mock data)
- If blocked: document WHY, update pitfall file, move on
- Update GAP STATE: UNTOUCHED → IN PROGRESS → PARTIAL → OPERATIONAL → SYSTEMIZED

## Resource Management

### Model Scheduling by Day
| Day | Agent | Model | VRAM |
|-----|-------|-------|------|
| Monday | SOL | `qwen2.5-coder:7b` | ~5GB |
| Tuesday | CODY | `qwen2.5-coder:7b` | ~5GB |
| Wednesday | ASSEMBLY/PESSI | `qwen2.5-coder:7b` | ~5GB |
| Thursday | PESSI | `qwen2.5-coder:7b` or `qwen3.5:9b` | ~5-7GB |
| Friday | CHATTY | None (research/web) | 0GB |
| Saturday | GENI/ATLAS | None (cloud/research) | 0GB |
| Sunday | VALI/JURIS | None or `qwen2.5-coder:7b` | 0-5GB |

### Special Rules
- **GENI (Saturday):** NEVER local video/image generation. Use cloud: Kling (lifetime), Runway (855 credits), Canva API.
- **PESSI (Thursday):** May use `qwen3.5:9b` (6.6GB) for architecture reasoning. Only when Ollama idle.
- **CODY (Tuesday):** Build/codegen tasks may need heavier model. Unload others first if needed.
- **ATLAS (Saturday, even weeks):** Knowledge work — may use no model at all (organizational tasks).

### Disk Space Warning
- Current: 154GB used / 35GB free (82% full)
- Clean up test artifacts after validation
- Use external storage for large outputs

## What Got Removed
- ❌ ORACLE from daily rotation (curriculum designer, not learner)
- ❌ Basic topics: "Error Alerting," "Credential Management," "Webhook Idempotency"
- ❌ 2-week schedule (too short)

## What Stays
- ✅ 10 agents in fleet (all accounted for)
- ✅ OpenClaw Release Monitor (9 AM daily)
- ✅ iCloud wiki sync (hourly)
- ✅ Memory Dreaming Promotion (3 AM daily)
- ✅ Wiki Bridge Auto-Sync (3:58 AM daily)
- ✅ RAG Auto-Sync (hourly)
- ✅ Fleet reviews (June 29)

## Resource Model
- **Before:** 7 agents, 15 min, basic topics, timeouts
- **After:** 10 agents, 30 min, novel topics, resource-aware, bi-weekly flexibility
- **Weekly output:** 7 comparative research + build artifacts
- **Monthly output:** ~28 artifacts, ~7 SYSTEMIZED capabilities

---
*Updated: 2026-06-20 02:04 CDT*
*Replaces: June 8 rotation (7 agents, basic topics)*
*Key changes: SOL/CODY/ATLAS weekly Mon-Wed, all others bi-weekly Thu-Sun*
