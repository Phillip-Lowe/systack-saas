# Nate's Context Window Assembly Method — Key Insights

**Source:** Nate's weekly AI update video (transcript provided by user)
**Date:** 2026-06-05
**Relevance:** HIGH — directly applicable to OpenClaw agent workflows, especially Aider + local file operations

---

## Core Method: Context Window Assembly via Natural Language File Discovery

### The Workflow

1. **Ask agent to scan file system** — "Look at my file system overall"
2. **Describe files in natural language** — NOT by filename or path
   - "This is about when I made it"
   - "This is about [topic], can you find it?"
3. **Agent finds and copies files** to a clean working folder
4. **Open new chat** pointing at that working folder
5. **Task execution** with clean context window

### Why It Works

- Codecs (GitHub Copilot's coding agent) evolved from GitHub sandbox → local file system
- Code files and text files are structurally the same to the model
- Agent can "figure out how files go together" when they're in a folder
- Clean folder = clean context window = better long-context performance

### Scale Achieved

- **30,000–50,000 word documents** — "very easily"
- Complex spreadsheet work
- Complex coding work
- Multi-threaded parallel idea incubation

---

## Prompting Evolution: From Task Execution → Collaborative Definition

### Three Eras of Prompting (per Nate)

| Era | Period | Approach |
|-----|--------|----------|
| Pre-2025 | Before Dec 2024 | Prompt engineering — structure, order, right stuff in right place |
| Agentic Phase | Dec 2024–Apr 2026 | "Here's your task, here are files, go get it done, here's what good looks like" |
| Collaborative Phase | May 2026–now | "Here are meaningful questions about standards. Help me define the shape of the task first, THEN execute agentically" |

### The Shift

**Old pattern:**
```
"Here's your task → go do it → here's how you know it's good"
```

**New pattern:**
```
"Here are my standards (as meaningful questions)
 Here are files I think are relevant
 Help me define the shape of this task first
 Once defined → now go execute agentically"
```

### Why the New Pattern Works Better

- **Claude 5.5 specifically** — "doesn't get lost when you shift gears and say 'Now go do it'"
- Feels like "true back-and-forth collaborativeness"
- Can be "messy" in the definition phase
- Model maintains coherence across the phase transition

---

## Why Codecs Specifically (vs Claude Code / Claude Co-Work)

- **File system handling** — Codecs evolved from GitHub repo sandbox, naturally handles folder structures
- **Auto-review system** — "good guardrails around long-running tasks"
- **Model stamina** — stays on task for long periods
- **Claude 4.7** — may have compute shortage or less solid long-context than 5.5

Nate's stance: "I don't need any particular team to win. I just want to get more efficient at working."

---

## Applicability to OpenClaw/Systack

### Immediate Applications

1. **Aider workflow enhancement** — Before spawning Aider, assemble context folder with natural language file discovery
2. **Invoice parser** — "Find all invoice-related files from last 2 weeks" → assemble → process
3. **Utopia Deli** — "Find menu files, order logic, n8n workflows" → assemble → modify
4. **Systack site** — "Find all service page content, case studies, pricing" → assemble → rewrite

### Implementation Notes

- OpenClaw has `dir_list`, `dir_fetch`, `read` tools for file discovery
- Could build a "context assembly" skill that:
  1. Takes natural language description
  2. Scans workspace recursively
  3. Finds relevant files by content (not just name)
  4. Copies to temp working folder
  5. Spawns Aider or direct agent on that folder

### Key Insight for Local Setup

Nate's method is **100% local** — file system operations, no cloud dependency. Fits Systack's local-first architecture perfectly.

---

## Next Steps / Experiments

1. **Test natural language file discovery** with OpenClaw tools
   - "Find all files about invoice parsing"
   - "Find files I worked on last week about n8n"
   - Compare results to manual navigation

2. **Build context assembly skill** — automated version of Nate's workflow
   - Input: natural language description + task
   - Output: clean working folder + spawned agent

3. **Test collaborative prompting pattern** — define first, execute second
   - Current: "Fix the bug in this file"
   - New: "What standards should this fix meet? What could go wrong? Define the fix first, then apply it"

4. **Aider integration** — use context assembly before spawning Aider
   - Pre-populate context folder
   - Aider gets clean workspace instead of full repo

---

## Tags
#context-windows #prompting #ai-workflows #local-first #aider #file-management #agent-collaboration
