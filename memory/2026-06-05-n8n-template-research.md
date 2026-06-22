# 2026-06-05 — n8n Template Library Research

## What We Did
Scanned n8n template library (9,875 templates) for patterns useful to:
1. Utopia Deli order system
2. Systack business operations
3. Personal Agent product
4. Invoice parser service

## Key Findings

### No Restaurant/Food Templates Exist
- 0 templates for restaurant ordering
- 0 templates for Square payment integration
- Deli system is genuinely custom/pioneering

### No Square Integration Templates
- Our Square payment link creation is entirely custom
- HTTP Request node with manual payload construction
- This is actually a competitive advantage

### Closest Analog: Concert Ticket Booking (#13453)
- Structurally identical to deli: webhook → validate → route → email → log
- Uses AI agent for validation (we could add this)
- Has audit trail + SLA escalation patterns we can adopt

### Patterns We Should Adopt
1. **Merge nodes after parallel branches** — we need this in deli V2/V3
2. **AI agent for validation/classification** — replace manual validation
3. **Multi-channel alerts** — Email + Telegram + Slack for different urgency
4. **GitHub backup** — version control for all workflows (#1534)
5. **Monitoring** — uptime checks for all services (#11763)

### Templates for Systack Services

| Service | Template | Application |
|---------|----------|-------------|
| Lead Generation | #2605 Google Maps | Find local prospects |
| Cold Outreach | #5691 LinkedIn + Claude | Personalized sales emails |
| Invoice Parser | #11811 LlamaCloud | PDF extraction enhancement |
| Personal Agent | #8237 Life Manager | Full agent architecture |
| Content Marketing | #16062 SEO Blog | Automated blog posts |
| Social Media | #16066 X Scheduler | Post scheduling |
| Monitoring | #11763 Uptime | Service health checks |
| Backup | #1534 GitHub | Workflow version control |

## What We Built
- `n8n-template-research.md` — Full research document (17,750 bytes)
- `SYSTACK-AUTOMATION-TEMPLATES.md` — Curated templates for deployment
- 2 new files in `~/utopia-deli-revamp/workflow-study/`

## Next Actions
1. Deploy GitHub backup (#1534) for all workflows
2. Deploy uptime monitoring (#11763) for systack.net
3. Study Personal Life Manager (#8237) for agent architecture
4. Add Merge nodes to deli V2 where parallel branches exist
5. Consider AI validation agent for deli orders (like #13453)

## Lessons
- Template library has no drop-in solutions for our use case
- But architectural patterns are universal and well-tested
- 2025-2026 trend is AI agents with memory — Systack is well-positioned
- Multi-channel alerting (Email + Telegram + Slack) is standard now
