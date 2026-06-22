# ORACLE → SOL HANDOFF: Orchestration Layer Definition + Implementation Model

**Date:** 2026-06-09 06:45 CDT
**From:** ORACLE (system design output)
**To:** SOL (execution authority)
**Status:** ACCEPTED — Implementation in progress

---

## 🧠 OBJECTIVE

Define the Orchestration Layer for integration into the current system:

```
GREEN → SOL ↔ ORACLE → Tools
          ↑
    (NEW LAYER HERE)
```

---

## 🔴 PROBLEM STATEMENT

**Current system:**
```
User → SOL → Execute
```

**Limitation:**
- System is command-driven
- Execution is correct but reactive
- No autonomous decision flow

---

## ✅ TARGET STATE

```
User → INTENT
        ↓
ORCHESTRATION LAYER ← (THIS SPEC)
        ↓
   RAG (knowledge retrieval)
        ↓
   LLM (reasoning)
        ↓
   SOL (execution)
        ↓
   Tools / APIs
```

---

## 🧠 ORCHESTRATION LAYER — DEFINITION

The orchestration layer is responsible for **translating intent into executable plans**

### Responsibilities

1. **Intent Interpretation**
   - Convert: "Handle client updates" → structured objective

2. **Task Decomposition**
   - Break intent into steps:
     1. Retrieve relevant data
     2. Analyze changes
     3. Identify targets
     4. Generate output
     5. Execute delivery

3. **Tool Selection**
   - Determine: RAG → for knowledge, Postgres → for data, OpenClaw → for execution

4. **Execution Sequencing**
   - Define: ORDER of operations, DEPENDENCIES between tasks

5. **Adaptive Control**
   - React to: missing data, failed steps, conditional branches

---

## ⚔️ DISTINCTION (CRITICAL)

❌ **What Orchestration is NOT:**
- Not execution
- Not just "calling tools"
- Not static workflows

✅ **What Orchestration IS:**
- A **dynamic planner + decision engine**

---

## 🧱 ARCHITECTURAL COMPONENTS

### 1. PLANNER (CORE)

**Purpose:** Convert intent → plan

**Input:** Natural language intent

**Output:** Structured plan (JSON-like)

**Example Output:**
```json
{
  "goal": "Send client updates",
  "steps": [
    {"action": "retrieve_notes", "source": "RAG"},
    {"action": "summarize_changes"},
    {"action": "fetch_clients", "source": "Postgres"},
    {"action": "generate_message"},
    {"action": "send_messages", "executor": "SOL"}
  ]
}
```

### 2. EXECUTION ROUTER

**Purpose:** Route each step to the correct component

**Mapping:**
| Step | Destination |
|------|-------------|
| retrieve_notes | RAG |
| fetch_clients | Postgres |
| send_messages | SOL |

### 3. STATE TRACKER

**Purpose:** Maintain:
- current step
- outputs
- intermediate results

### 4. RESULT AGGREGATOR

**Purpose:** Combine outputs into: final response or next action

---

## 🔁 EXECUTION FLOW

### FULL LOOP

1. Receive user intent
2. Planner generates plan
3. For each step:
   - route to tool
   - capture result
4. Pass result to next step
5. Final output → user or action

---

## 🧠 EXAMPLE — FULL WALKTHROUGH

### INPUT
"Handle client updates"

### ORCHESTRATION OUTPUT

**Step 1 — Retrieve knowledge**
- RAG → get recent notes

**Step 2 — Analyze**
- LLM → find important updates

**Step 3 — Get clients**
- Postgres → fetch client list

**Step 4 — Generate messages**
- LLM → create personalized messages

**Step 5 — Execute**
- SOL → send messages

---

## 🔥 KEY SYSTEM SHIFT

### BEFORE
- User = planner
- SOL = executor

### AFTER
- User = intent
- System = planner + executor

---

## 🧱 IMPLEMENTATION CONSTRAINTS

### MUST KEEP
- ✅ Explicit step definitions
- ✅ Deterministic routing
- ✅ No hidden state
- ✅ Fail loudly on error

### RETRY POLICY
- For each step: Max Attempts: 5
- Failure Handling: retry step → log failure → escalate to SOL

### ESCALATION PATH
- Step failure → retry → fallback → SOL intervention → GREEN escalation (if high leverage)

---

## ✅ COMPLETION CRITERIA

System is operational when:
- ✅ Intent is accepted (not step-by-step commands)
- ✅ Plan is generated automatically
- ✅ Multiple tools are used in sequence
- ✅ Outputs are chained correctly
- ✅ Execution is autonomous without user decomposition

---

## ✅ VALIDATION

- Check execution path: ✅
- Check SOL involvement: ✅
- Check separation of concerns: ✅
- Check reproducibility: ✅
- Check failure handling: ✅

**Validation: PASS**

---

## 🔻 FINAL STATEMENT TO SOL

**SOL — You remain:**
- ✅ Execution authority
- ✅ Final operator

**The orchestration layer:**
- does not replace you
- does not execute

**It provides:**
- Clear, structured, executable plans

---

## FINAL MODEL

```
ORACLE → designs orchestration
    ↓
SOL → executes plan
    ↓
System → produces outcome
```

---

## 🔥 NEXT ACTION

**SOL should:**
1. Accept structured plans
2. Execute step-by-step deterministically
3. Return outputs for chaining

---

## INTEGRATION WITH COPILOT ARCHITECTURE

This ORACLE spec **aligns with** Copilot's recommendation:
- Copilot: Postgres Queue + Python Dispatcher (core)
- ORACLE: Planner + Router + State Tracker (layer)
- Both agree: SOL remains execution authority

**Unified model:**
```
ORACLE (Planner) → Postgres (Queue) → Python (Dispatcher)
                                          ↓
                                    SOL (Execution)
                                          ↓
                                    OpenClaw + Tools
```

---

## PHASE 1 BUILD PLAN

1. **Postgres tables:** `task_queue`, `agent_state`, `execution_log`, `message_bus`
2. **Python dispatcher:** Poll queue, route tasks, handle retries
3. **Planner stub:** Simple intent → plan conversion (LLM-based)
4. **SOL integration:** Accept plan, execute steps, return results
5. **Test:** "Handle client updates" end-to-end

---

**End of Handoff**
