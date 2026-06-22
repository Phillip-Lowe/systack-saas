# CRITICAL: Memory Search Down - 2026-06-13

**Time:** 04:29 CDT
**Status:** 🔴 BLOCKING - Memory search completely non-functional
**Impact:** High - Agent cannot search MEMORY.md or wiki during sessions

## Error
```
Ollama embed HTTP 404: {"error":"model \"nomic-embed-text\" not found, try pulling it first"}
```

## Root Cause
The `nomic-embed-text` embedding model is not installed in Ollama. This is required for memory_search and wiki_search to function.

## Fix Required
Run:
```bash
ollama pull nomic-embed-text
```

Then verify:
```bash
ollama list | grep nomic-embed-text
```

## Impact During This Session
- Could not search memory before acting (violated AGENTS.md RULE 1)
- Had to fall back to file-based reads (less efficient)
- Could not verify wiki status or search wiki for existing knowledge

## Priority
**P0 - Fix immediately at start of next session**

## Next Session Action
1. Run `ollama pull nomic-embed-text`
2. Verify with `ollama list`
3. Test with `memory_search query="test"`
4. If still failing, check Ollama service status

## Related
- Embedding provider config may need updating
- Consider fallback embedding model if nomic-embed-text unavailable
