# Context as Infrastructure — Fleet Translation

**Source:** Nate's AI workflow video, translated for Systack fleet architecture  
**Date:** 2026-06-05  
**Status:** Ready to implement

---

## The Real Insight: Environment Engineering > Prompt Engineering

Nate's method isn't about "use Codecs." It's about **treating context like infrastructure instead of stuffing it into a prompt.**

We're already ahead on this — schema-driven state, externalized sources, deterministic flows. The upgrade is combining strict production systems with flexible AI workspaces for design and iteration.

---

## 1. Context as a System (not a prompt)

### What Nate does
Builds a clean working folder = controlled context window. Lets model reason across structured files instead of one mega-prompt.

### Fleet mapping — Utopia Deli
Instead of long Code node prompts, create:

```
/order_run/
  ├── cart_state.json
  ├── menu_schema.json
  ├── modifiers_map.json
  ├── tax_rules.json
  ├── task_instructions.txt
```

Then point AI at the folder, not a prompt. Let it reason across files like a repo.

**Reduces:** ghost items, pricing inconsistencies, state drift across passes.  
**Supports:** `pass_index` + `FREEZE_CART_STATE` multi-pass fix (paused 2026-06-04).

---

## 2. Natural-Language Retrieval Over Strict Naming

### Nate's point
"I describe what the file is about... it finds it."

### Fleet upgrade
Stop: `CITY_TAX_TABLE_FINAL_V2_FIXED`  
Start: `Arkansas city tax rates with county matching and totals`

**Aligns with:** AI-driven lookup nodes, dynamic workflow routing, less brittle naming.

---

## 3. Prompt Shift: Command → Collaboration

| Mode | When |
|------|------|
| **Old:** "Do this task with these files. Here's what good looks like." | Execution (still valid) |
| **New:** "Here's what I'm trying to achieve. Here are constraints. Help define the shape of the problem." | Design phase |

### Where to apply in fleet
✅ Flow design, schema design, pricing model logic — before building nodes  
❌ NOT during order execution — still need strict invariants there

**Unlocks:** Design phase acceleration without losing deterministic production safety.

---

## 4. Multi-Threading → Pass-Based Folder Structure

### Nate's version
Multiple prompts running sequentially/in parallel.

### Fleet version
```
/run_context/
  ├── pass_1/          # frozen state, evaluated independently
  ├── pass_2/
  ├── pass_3/
```

Each pass: frozen state, no cross-pass contamination. Solves upsell misclassification, state overwrites.

**Stronger version:** n8n orchestration layer with multiple Code nodes as threads:
- pricing validator
- cart normalizer
- upsell evaluator
- tax calculator

---

## 5. Autonomy + Guardrails

### Nate: "autoreview + guardrails"

### Fleet equivalent
Already have: IF nodes, validation steps, failure-on-rule violations.

**Upgrade:** Add AI validation layer — "AI QA node" before every critical commit:
- cart integrity check
- pricing alignment check
- tax correctness check

---

## What DOESN'T Apply

❌ "Just let AI handle it" — payments must be deterministic, no drift/hallucination  
❌ Long context dumping — we already solved this with schemas + structured sheets + state machine. Don't regress.

---

## Immediate Upgrades (Ready to Build)

### 1. Context Folder Pattern
For: pricing debugging, order simulation, workflow testing.

### 2. AI "Design Mode"
Before any new logic: "Here's what I want. Here's current behavior. Help me define structure before implementation."

### 3. Pass-Based Folder Structure
Plug directly into paused `pass_index` + `FREEZE_CART_STATE` fix.

### 4. AI Validation Node (optional)
Post-pricing check, post-merge check in n8n.

---

## Next Action

**Paused fix from 2026-06-04:** Multi-pass cart fix (`pass_index` + `FREEZE_CART_STATE`)  
**This translation enables:** Resume with proper context-as-infrastructure pattern instead of fighting context window limits in Code nodes.

Ready to translate into drop-in n8n pattern or full folder + `pass_index` implementation spec.

---

## Tags
#context-infrastructure #fleet-architecture #n8n #state-machine #design-mode #multi-pass #cart-fix
