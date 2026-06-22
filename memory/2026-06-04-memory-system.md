# 2026-06-04 — Memory System Overhaul

## What Changed

Implemented the full memory enforcement plan from GREEN-COPILOT.

### Files Updated

**AGENTS.md** — Complete rewrite with enforcement layer:
- RULE 1: Memory Retrieval is MANDATORY
- RULE 2: MEMORY.md is Source of Truth (over chat)
- RULE 3: Execution Guard (retrieve → plan → approve → execute)
- RULE 4: No Guessing (ask if uncertain)
- RULE 5: Document Everything
- Added tiered memory system table
- Added execution guard for high-leverage actions

**MEMORY.md** — Restructured with:
- Memory System Rules section
- Maintenance Schedule
- Promotion Rule (daily → weekly → long-term)
- All previous content preserved and reorganized

**openclaw.json** — Added memory configuration:
```json
{
  "agents": {
    "defaults": {
      "memorySearch": {
        "enabled": true,
        "sources": ["memory", "sessions"],
        "experimental": { "sessionMemory": true },
        "provider": "ollama",
        "model": "nomic-embed-text",
        "query": {
          "hybrid": {
            "enabled": true,
            "vectorWeight": 0.7,
            "textWeight": 0.3
          }
        }
      },
      "memoryFlush": {
        "enabled": true,
        "softThresholdTokens": 40000,
        "prompt": "Distill decisions, rules, lessons learned...",
        "systemPrompt": "Only store important long-term knowledge..."
      },
      "contextPruning": {
        "mode": "cache-ttl",
        "ttl": "6h",
        "keepLastAssistants": 3
      }
    }
  }
}
```

### Technical Changes

1. **Installed nomic-embed-text** via Ollama (274MB)
   - Purpose: Local embedding model for memory search
   - No cloud dependency for memory retrieval

2. **Configured hybrid search**
   - Vector weight: 0.7 (semantic similarity)
   - Text weight: 0.3 (exact match)
   - Sources: memory files + session transcripts

3. **Set memory flush rules**
   - Soft threshold: 40k tokens
   - Distill only important knowledge
   - Ignore routine details

4. **Context pruning**
   - TTL: 6 hours
   - Keep last 3 assistant messages
   - Prevents context rot

### Verification

- memory_search now works with nomic-embed-text ✅
- Returns results from MEMORY.md ✅
- No more "model not found" errors ✅

### Next Steps

1. Test memory retrieval during next session
2. Verify rules are actually being followed
3. Monitor for over-retrieval or context bloat
4. Adjust thresholds if needed

### Status: ACTIVE

The memory system is now production-ready with mandatory retrieval, proper flushing, and local embeddings.
