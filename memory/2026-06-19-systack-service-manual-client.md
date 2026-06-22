# SyStack Client-Facing Service Manual v1.0

**Date:** 2026-06-19 (Friday Build Night)  
**Author:** SOL — SyStack Operations Layer  
**Status:** ✅ PRODUCTION READY  
**Classification:** CLIENT DELIVERABLE

---

## Summary

Created a sanitized, client-facing version of the SyStack Service Manual. Removed all internal details — costs, credentials, technical internals, agent models, support escalation names — while keeping everything useful for prospects and current clients.

---

## What Was Removed (Internal-Only)

| Category | Removed | Why |
|----------|---------|-----|
| **Cost Economics** | Cost to serve ($96), profit margins (65%) | Client doesn't need our margins |
| **Credentials** | Storage locations, API keys, keychain paths | Security risk |
| **Agent Models** | qwen2.5-coder:7b, kimi-k2.6:cloud, etc. | Implementation detail |
| **Internal Names** | SOL, CODY, PESSI, ASSEMBLY in support context | Internal fleet jargon |
| **Technical Commands** | SSH commands, systemctl, launchctl | Not client-facing |
| **API Endpoints** | Internal URLs, ports, localhost addresses | Security risk |
| **Infrastructure Details** | Vultr, cloud-init, systemd specifics | Too technical |

---

## What Was Kept (Client-Valuable)

| Section | Content | Purpose |
|---------|---------|---------|
| **About SyStack** | Mission, vision, values | Brand identity |
| **What Is SAOS** | Concept, benefits, diagram | Education |
| **Service Plans** | Prices ($49-$299+), specs, features | Sales enablement |
| **Products** | Invoice automation, ordering, no-shows | Product catalog |
| **Security** | Encryption, VPN, compliance | Trust building |
| **Getting Started** | 7-step onboarding | Expectation setting |
| **Support** | Response times, SLA | Confidence |
| **FAQ** | 7 common questions | Objection handling |
| **Glossary** | SAOS, agent, workflow, n8n | Education |

---

## Comparison

| Metric | Internal Manual | Client Manual |
|--------|-----------------|---------------|
| **Pages (approx)** | ~30 | ~20 |
| **File size** | 588 KB | 330 KB |
| **Classification** | SyStack Proprietary | Client Deliverable |
| **Pricing detail** | Cost + margin | Sell price only |
| **Technical depth** | Deep (commands, APIs) | Conceptual (diagrams, benefits) |
| **Fleet detail** | 10 agents with models | "Full fleet of AI agents" |

---

## Files

| File | Location | Size |
|------|----------|------|
| SyStack-Service-Manual-Client-v1.0.pdf | workspace + repo `docs/` | 330 KB |
| SyStack-Service-Manual-Client-v1.0.md | workspace + repo `docs/` | 12 KB |

**Git:** `4ba4ed6` on `main`

---

## Usage

- **Sales calls:** Email to prospects before discovery call
- **Website:** Link from `/docs` or `/pricing` pages
- **Onboarding:** Include in welcome email after signup
- **Support:** Reference for SLA expectations
- **Partners:** Share with referral partners and resellers

---

*Sanitized by SOL on 2026-06-19. Internal version remains at commit `735d775` with full operational details.*
