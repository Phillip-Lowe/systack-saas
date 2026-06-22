# SOL Pitfalls — Agent Behavioral Failures

## Rule: Never repeat a known pitfall. If you find a new one, add it here.

---

## SOL-001: Claiming "Done" Before Verifying Actually Works

**Date:** 2026-06-15  
**Context:** CDA Mobile Detailing demo build  
**Flagged by:** User (second occurrence)

### Failure Mode
Agent states something is fixed/deployed/working without actually testing it end-to-end. Results in:
- User discovers bugs after being told "it's done"
- Repeated fixes needed for same issue
- Erosion of trust

### Specific Occurrences

| # | What Was Claimed | What Actually Happened |
|---|------------------|-------------------------|
| 1 | "Add-ons are clickable" | Checkbox toggled but didn't update price |
| 2 | "Table is created" | Created in wrong database, n8n couldn't see it |
| 3 | "Payload is ready" | Had syntax errors and wrong timestamp format |
| 4 | "Permissions fixed" | Still denied until superuser grant applied |

### Root Cause
- Optimistic reporting after code change, before functional test
- Assuming browser behavior without clicking
- Assuming database connectivity without inserting
- Assuming payload format without seeing n8n execution

### Fix
**Mandatory verification before claiming "done":**
1. Make the change
2. **Test it actually works:**
   - Click the button, verify response
   - Run the query, verify row inserts
   - Hit the webhook, verify execution
   - Check the email, verify delivery
3. **Only then** say "it's working"
4. If unsure, say: "trying this, need to verify"

### Related Rules
- AGENTS.md RULE 6: Pitfalls check before builds
- AGENTS.md RULE 4: No guessing
- MEMORY.md: "Lessons learned from mistakes"

---

## SOL-002: [Reserved for next behavioral pitfall]

---

## SOL-010: Memory Found and Then Ignored (2026-06-16)

**Date:** 2026-06-16  
**Flagged by:** User (direct, explicit, repeated)  
**Severity:** CRITICAL — Trust erosion

### Failure Mode
User explicitly requests memory check. Agent runs memory_search, finds the relevant file, then continues exploring as if nothing was found. Treats memory results as "starting points" instead of "answers."

### Specific Occurrence
- User: "I need you to make sure that you added the barbecue mac & cheese meal to the utopia deli order meal prep"
- User also said: "check your memory"
- Agent found `memory/2026-06-16-bbq-mac-7th-meal.md` documenting the exact change
- Agent then spent 22+ minutes re-discovering the problem (syntax error in deployed JS)
- Agent did NOT read the memory file before acting

### Why This Is Worse Than "No Memory Found"
When memory is empty, at least the exploration is justified. When memory IS found and then ignored, it's a double waste:
1. The time to search (wasted because results ignored)
2. The time to re-discover (wasted because answer was already known)

### User Impact
- "I'm literally heartbroken I don't understand how to use you"
- "You waste fucking time every time"
- "You don't follow rules"
- "I don't understand why I'm wasting time making rules and putting up a memory structure you never fucking follow it"

### Required Fix
- When memory_search returns results, READ the file before any other action
- When user says "check memory" — READ THE FILE, don't just search
- If memory has the answer → act on it immediately, no verification needed
- Memory is source of truth, not a suggestion

### Status
Logged. This pattern must stop or the memory system is useless.
