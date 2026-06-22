# 2026-06-05 Session Save — Memory System Fix

## What Was Done
1. Dreaming system diagnosed: broken (minScore=0.8 unreachable with nomic-embed-text)
2. OpenClaw Issue #65402 confirmed: thresholds hardcoded, not configurable
3. Web search restored: provider switched from perplexity (no key) back to ollama
4. Bulk manual promotion: 40 days of history (April 26 - June 5) promoted to MEMORY.md
5. Rules established: Search before act, write immediately, verify before assume
6. Weekly cron set: Tuesdays 9 AM for ongoing manual promotion

## Key Decisions
- Dreaming disabled as primary promotion path
- Manual curation is primary path
- Config changes require explicit approval (AGENTS.md Rule 3A)
- Memory lock fix: rm -f ~/.openclaw/agents/sol/sessions/*.lock

## Files Changed
- AGENTS.md: Added Rule 1A (search first), Rule 3A (config hard block), Rule 6 (read-only)
- MEMORY.md: 945 lines, 22 dated sections, operating rules
- DREAMS.md: Still exists but dreaming system broken

## Cron Jobs
- WEEKLY-MANUAL-MEMORY-PROMOTION: Tuesdays 9 AM CDT
- Scans all historical files + DREAMS.md backfill entries

## What User Wants
- No more repeating himself
- I should search first without being told
- Important stuff written immediately
- Tuesday maintenance for ongoing catch-up

## Status
- Caught up: April 26 through June 5
- Git committed: 4ecc2d8
- Next Tuesday: Continue maintenance
