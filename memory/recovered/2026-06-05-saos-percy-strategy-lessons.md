# Strategy Lesson: RAM + Local Models + Proprietary Data = Infrastructure Reality Check

**Date:** 2026-06-05 (evening reflection)
**Context:** Post-Percy deployment, planning SAOS foundation
**Author:** Phillip Lowe / Sol

---

## The Mistake We Made

We treated Percy (and by extension, every future client agent) as a simple "deploy and go" service.

We **underestimated** three critical factors that change everything:

1. **RAM requirements** — 4GB VPS is NOT enough for production identity files + model + OS
2. **Local-only mandate** — Some clients CANNOT have data leaving their infrastructure (proprietary info, HIPAA, internal data)
3. **Cost implications** — These constraints drive price. We can't quote without understanding them.

---

## Factor 1: RAM is Non-Negotiable

### What We Learned (Hard Way)

Percy on 4GB VPS:
- System prompt (identity files + OpenClaw bootstrap): ~1,250 tokens (stripped), ~8,800 tokens (full)
- qwen2.5:3b with 16K context: ~2.5GB RAM
- OS + Gateway: ~1GB RAM
- **Total: 3.5GB used, 0.5GB free**
- Result: Swapping, 2+ minute response times, timeouts

### The Math

| VPS RAM | Model | Context | Model RAM | OS+Gateway | Total | Free | Status |
|---------|-------|---------|-----------|------------|-------|------|--------|
| 2GB | qwen2.5:1.5b | 8K | ~1.2GB | ~0.8GB | 2.0GB | 0GB | ⚠️ Tight, no headroom |
| 4GB | qwen2.5:3b | 16K | ~2.5GB | ~1.0GB | 3.5GB | 0.5GB | ⚠️ Works, slow |
| 8GB | qwen2.5:7b | 32K | ~3.5GB | ~1.0GB | 4.5GB | 3.5GB | ✅ Comfortable |
| 16GB | qwen2.5:14b | 64K | ~7.5GB | ~1.0GB | 8.5GB | 7.5GB | ✅ Premium |

**Rule:** Always leave 2-3GB headroom for:
- Other processes (Tailscale, Ollama daemon)
- Spike loads (multiple concurrent requests)
- Future growth (bigger identity files, more tools)

---

## Factor 2: Local-Only Mandate

### The Problem

Some clients have data that **cannot** hit external servers:
- **McDonald's/Jacqueline:** Internal schedules, employee data, financials
- **Healthcare clients:** HIPAA-protected patient data
- **Legal clients:** Attorney-client privileged documents
- **Financial clients:** Trading data, client portfolios
- **Government:** Classified or sensitive operations

### The Implication

These clients need:
1. **On-premise deployment** (not cloud VPS)
2. **Air-gapped models** (no cloud API calls)
3. **Local file storage only** (no sync to external services)
4. **Tailscale or VPN** (secure access, no public endpoints)

### What This Means for Systack

| Deployment Type | Requirements | Cost | Use Case |
|-----------------|--------------|------|----------|
| Cloud VPS | Standard internet, Tailscale | $20-80/mo | Small business, public data |
| On-premise server | Physical hardware at client site | $500-2000 hardware + $100/mo maintenance | Healthcare, legal, enterprise |
| Air-gapped laptop | No network, local models only | $1000-3000 hardware | Intelligence, research |
| Hybrid (cloud + local) | Some data local, some cloud | $50-150/mo | Most businesses |

**We need to ask EVERY prospect:**
> "Do you have any data that cannot leave your premises or hit external servers?"

If YES → on-premise quote. If NO → cloud VPS quote.

---

## Factor 3: Cost Implications

### Current Pricing (Underestimated)

| Component | Cost | Notes |
|-----------|------|-------|
| Cloud VPS (4GB) | $20/mo | Too small, demo only |
| Cloud VPS (8GB) | $40/mo | Minimum production |
| Cloud VPS (16GB) | $80/mo | Comfortable production |
| On-premise hardware | $500-2000 one-time | NUC, mini PC, old server |
| On-premise maintenance | $50-100/mo | Remote access, updates |
| Systack setup fee | $200-500 | One-time (currently free for beta) |
| Systack monthly | $50-150/mo | Monitoring, support, updates |

### Total Monthly Cost Per Client

| Scenario | VPS | Systack | Total | Annual |
|----------|-----|---------|-------|--------|
| Small business (cloud 8GB) | $40 | $50 | $90/mo | $1080 |
| Medium business (cloud 16GB) | $80 | $100 | $180/mo | $2160 |
| Enterprise (on-premise) | $75 | $150 | $225/mo | $2700 |
| Budget (cloud 4GB, stripped) | $20 | $50 | $70/mo | $840 |

**We were thinking $20-40/mo total. Reality is $90-225/mo.**

This changes our market positioning and sales conversations.

---

## Factor 4: SAOS Foundation Impact

### What is SAOS?

The **Systack Agent Operating System** — the foundation layer all Systack agents run on.

It needs to account for:
1. **Variable RAM environments** — 2GB to 16GB+
2. **Local-only mode** — no external dependencies
3. **Data sensitivity tiers** — public, internal, confidential, restricted
4. **Deployment flexibility** — cloud, on-premise, air-gapped, hybrid

### SAOS Design Changes Required

| Feature | Original Plan | Revised Plan |
|---------|--------------|--------------|
| Model management | Cloud API fallback | Local-only by default, cloud opt-in |
| File storage | Gateway sync | Local storage, no sync |
| Authentication | Password or token | Tailscale only (no passwords in config) |
| Updates | Automatic via gateway | Manual or scheduled, client-controlled |
| Monitoring | External health checks | Local logs + client-visible dashboard |
| Backup | Cloud backup | Local backup + optional client-owned cloud |

### SAOS Data Sensitivity Tiers

```
Tier 1: Public
- Data: Public info, general knowledge
- Deployment: Cloud VPS OK
- Model: Cloud or local
- Cost: Lowest

Tier 2: Internal
- Data: Business schedules, employee info, non-sensitive docs
- Deployment: Cloud VPS with Tailscale
- Model: Local only
- Cost: Medium

Tier 3: Confidential
- Data: Financials, client lists, contracts
- Deployment: On-premise server or VPN
- Model: Local only, air-gapped
- Cost: High

Tier 4: Restricted
- Data: HIPAA, legal privilege, classified
- Deployment: Air-gapped, no network
- Model: Local only, no updates without approval
- Cost: Premium
```

---

## What We Must Do Differently

### 1. Discovery Questionnaire (Before Quote)

**Mandatory questions for every prospect:**

1. What types of data will the agent handle? (public / internal / confidential / restricted)
2. Does any data need to stay on your premises? (yes / no / not sure)
3. Do you have compliance requirements? (HIPAA / SOX / GDPR / none)
4. How many users will access the agent? (1 / 2-5 / 6-20 / 20+)
5. What's your expected conversation volume? (light / moderate / heavy)
6. Do you have existing servers/hardware we can use? (yes / no)
7. What's your budget range? ($50-100/mo / $100-200/mo / $200-500/mo / enterprise)

### 2. Deployment Tiers (Clear Packages)

| Tier | Name | RAM | Model | Deployment | Monthly |
|------|------|-----|-------|------------|---------|
| Bronze | Starter | 4GB | qwen2.5:3b | Cloud VPS | $70/mo |
| Silver | Professional | 8GB | qwen2.5:7b | Cloud VPS | $120/mo |
| Gold | Business | 16GB | qwen2.5:14b | Cloud VPS | $220/mo |
| Platinum | Enterprise | 8-16GB | Custom | On-premise | $300+/mo |
| Custom | Air-gapped | Variable | Custom | No network | $500+/mo |

### 3. Infrastructure Survey (Before Deployment)

**Physical deployments:**
- Site visit or video call to assess:
  - Existing hardware (can we reuse?)
  - Network setup (Tailscale compatibility)
  - Power and cooling
  - Security requirements (locked room, camera, etc.)

**Cloud deployments:**
- Remote assessment:
  - Internet speed (needs 10Mbps+ for Tailscale)
  - Firewall rules (can we open ports?)
  - Existing cloud accounts (AWS, Azure, GCP)

### 4. Documentation Updates

All deployment docs need these sections:

1. **RAM Requirements** — Minimum, recommended, optimal
2. **Data Sensitivity** — What tier this deployment is
3. **Local-Only Checklist** — What services are disabled
4. **Cost Breakdown** — VPS + Systack + maintenance
5. **Compliance Notes** — HIPAA, SOX, etc. if applicable

---

## Files That Need Updating

| File | Update Needed |
|------|--------------|
| `DEPLOYMENT-PLAYBOOK.md` | Add RAM sizing, data sensitivity tiers |
| `MODEL-CONTEXT-SIZING-GUIDE.md` | Expand to include on-premise, air-gapped |
| `PERCY-DEPLOYMENT-PLAN.md` | Add discovery questionnaire |
| `SYSTACK-PRODUCTION-PLAN.md` | Revise pricing, add deployment tiers |
| `systack-site/services/personal-agent.html` | Add pricing tiers, RAM requirements |
| `systack-site/services/invoice-extractor.html` | Clarify data handling (local vs cloud) |
| New: `SAOS-FOUNDATION-SPEC.md` | Full spec for agent operating system |
| New: `CLIENT-DISCOVERY-TEMPLATE.md` | Questionnaire template |

---

## Key Lesson

**"We must come in with a solid grounding and understanding of what it's gonna take from every angle to implement any project."**

No more guessing. No more "we'll figure it out." No more underestimating infrastructure.

Every deployment needs:
1. ✅ Data sensitivity assessment
2. ✅ RAM/context sizing calculation
3. ✅ Deployment type decision (cloud / on-premise / air-gapped)
4. ✅ Cost estimate with headroom
5. ✅ Compliance checklist
6. ✅ Client approval of all above

**Then** we deploy.

---

## Next Actions

1. [ ] Create `SAOS-FOUNDATION-SPEC.md` with all deployment tiers
2. [ ] Create `CLIENT-DISCOVERY-TEMPLATE.md` questionnaire
3. [ ] Update `systack-site` with pricing tiers and RAM requirements
4. [ ] Update all deployment playbooks with data sensitivity sections
5. [ ] Review Jacqueline's Percy — classify as Tier 2 (Internal), document in client folder
6. [ ] When quoting next client, use discovery questionnaire first

---

*Logged by Sol*
*Never underestimate infrastructure again*
