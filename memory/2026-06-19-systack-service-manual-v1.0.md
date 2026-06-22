# SyStack Service Manual v1.0 — Generated

**Date:** 2026-06-19 (Friday Build Night)  
**Session:** SOL + Phillip Lowe  
**Status:** ✅ COMPLETE

---

## What Was Built

### Comprehensive Branded Service Manual
A 588KB branded PDF covering the entire SyStack operation — from brand identity to technical operations.

### Five Parts, Ten Chapters

| Part | Chapter | Content |
|------|---------|---------|
| I — Brand Identity | 1. Who We Are | Mission, vision, values |
| | 2. Visual Identity | Color palette, typography, logo usage |
| | 3. Voice & Tone | Writing principles, tone by context |
| II — The Fleet | 4. Agent Architecture | 10 agents in 5 tiers |
| | 5. Agent Profiles | Individual agent roles, models, scope |
| III — Service Offerings | 6. SAOS Tiers | Starter ($49) to Enterprise (custom) |
| | 7. Product Catalog | Invoice automation, ordering, no-show prevention |
| IV — Operations | 8. Provisioning Procedure | 7-8 minute automated VPS deployment |
| | 9. Monitoring & Support | Health checks, support levels, on-call |
| | 10. Compliance & Security | JURIS framework, incident response |
| V — Appendices | A-E | Contacts, APIs, credentials, commands, changelog |

### Brand Elements

- **Palette:** Navy `#001a2d`, Teal `#007da9`, Cyan `#00a1db` (12 colors total)
- **Typography:** Helvetica Neue family, 10.5pt body, structured hierarchy
- **Voice:** Concise, direct, specific, honest, action-oriented
- **Classification:** SyStack Proprietary / Internal & Client-Facing

---

## Key Sections Included

### Fleet Coverage
| Agent | Role | Model | Scope |
|-------|------|-------|-------|
| SOL | Orchestrator | kimi-k2.6:cloud | Cross-fleet strategy |
| CODY | Build Engine | qwen2.5-coder:7b | Technical implementation |
| ASSEMBLY | Deployment | qwen3.5:9b | n8n, VPS, Tailscale |
| VALI | Validation | qwen3.5:9b | Quality gates, testing |
| PESSI | Risk | qwen3.5:9b | Security, stress analysis |
| ORACLE | Architecture | kimi-k2.6:cloud | Design, planning |
| ATLAS | Knowledge | qwen3.5:9b | Documentation, memory |
| CHATTY | Communication | qwen3.5:9b | Client-facing messaging |
| GENI | Creative | ComfyUI | Image/video generation |
| JURIS | Compliance | qwen3.5:9b | Legal, audit, governance |

### SAOS Pricing
| Tier | Price | VPS | Agents | Support |
|------|-------|-----|--------|---------|
| Starter | $49/mo | 1GB | 3 | Email |
| Professional | $149/mo | 4GB | 7 | Email + Chat |
| Business | $299/mo | 16GB | 10 | Priority + Phone |
| Enterprise | Custom | 32GB | 10+ | Dedicated + SLA |

### Support SLA
| Severity | Response | Resolution | Escalation |
|----------|----------|------------|------------|
| P0 (Down) | 15 min | 2 hours | SOL + Phillip |
| P1 (Broken) | 1 hour | 4 hours | ASSEMBLY + SOL |
| P2 (Degraded) | 4 hours | 24 hours | CHATTY + Support |
| P3 (Question) | 24 hours | 72 hours | CHATTY |

---

## Files Generated

| File | Location | Size |
|------|----------|------|
| SyStack-Service-Manual-v1.0.pdf | workspace + repo | 588 KB |
| SyStack-Service-Manual-v1.0.md | workspace + repo | 18 KB |

**Git commit:** `735d775` on `main` branch
**Repo:** github.com/Phillip-Lowe/systack-saas

---

## Brand Consistency

The manual uses the SyStack branded PDF generator pipeline:
- pandoc: Markdown → HTML
- pyppeteer: HTML → PDF with full CSS rendering
- SyStack color palette embedded in CSS
- Cover bar with gradient
- Proper typography and page layout

---

*Ready for client presentation, investor review, or team onboarding.*
*Edit the Markdown source and regenerate via:*
```bash
python3 generate_pdf.py input.md output.pdf --title "Title" --brand systack
```
