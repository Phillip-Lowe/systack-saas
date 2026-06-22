# Memory Dreaming System Issues - 2026-06-13

## Status
Two related memory promotion systems have problems:

### 1. Memory Dreaming Promotion (Daily 3 AM)
**Status:** ⚠️ Enabled but may not be working correctly
- Runs daily at 3 AM (`0 3 * * *`)
- Last run: 1781337600015 (2026-06-13 03:00 CDT)
- Status: `ok` but delivery mode is `none` (no visible output)
- **Issue:** May not be promoting effectively - hardcoded thresholds

### 2. Weekly Manual Memory Promotion (Tuesdays 9 AM)
**Status:** 🔴 ERROR - Last run failed
- Schedule: Tuesdays 9 AM (`0 9 * * 2`)
- Last run: 1781013735777 (2026-06-07)
- **Error:** "Delivering to BlueBubbles requires --to <handle|chat_guid:GUID>"
- Consecutive errors: 1

## Impact
- Memory promotion from daily logs to MEMORY.md may not be working reliably
- Agent may not have access to recent critical information at session start
- RAG Auto-Sync runs hourly but relies on memory being properly indexed

## Next Session Actions
1. Check if Memory Dreaming Promotion actually promoted today's critical issue
2. Fix weekly manual promotion delivery target (BlueBubbles config)
3. Consider running manual memory promotion if dreaming didn't work
4. Verify MEMORY.md has the critical memory search issue documented

## Related Files
- MEMORY.md (should have 2026-06-13 critical issue)
- memory/2026-06-13-memory-search-down-critical.md
- memory/2026-06-13-session-complete.md

## Fix for Weekly Promotion
The BlueBubbles delivery needs a proper chat_guid. Update cron config or switch to `none` delivery mode for isolated runs.
