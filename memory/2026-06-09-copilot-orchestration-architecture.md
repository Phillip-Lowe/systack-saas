# Copilot Consultation — Orchestration Architecture Decision

**Date:** 2026-06-09 06:21 CDT
**Consultant:** Microsoft 365 Copilot (ORACLE mode)
**Topic:** Multi-agent orchestration layer for Systack fleet
**Status:** ✅ Complete — Architecture validated

---

## ✅ EXECUTIVE VERDICT

**Recommended Architecture:**
> **D) Hybrid: Custom Postgres Queue + Python Dispatcher (CORE) + n8n (EDGE AUTOMATION)**

**DO NOT use OpenClaw TaskFlow as your core orchestrator.** It should remain a capability skill, not the system brain.

---

## 🧠 WHY THIS IS THE CORRECT MOVE

You are building a **real multi-agent operating system**, not just chaining workflows.

Your requirements:
- Task queue ✅
- State machine ✅
- Auto-dispatch ✅
- Failure recovery ✅
- Inter-agent messaging ✅

👉 n8n alone **cannot** handle this deterministically
👉 OpenClaw TaskFlow is **too high-level and opaque**

**You need a deterministic execution core.**

---

## 🏗️ FINAL ARCHITECTURE (CANONICAL)

```
┌────────────────────────┐
│      GREEN (You)       │
└──────────┬─────────────┘
           │
    ┌──────▼──────┐
    │    SOL      │
    │ Orchestrator│
    └──────┬──────┘
           │
    ┌───────────────────┼────────────────────┐
    │                   │                    │
┌───▼───────┐   ┌───────▼────────┐   ┌──────▼────────┐
│ Postgres  │   │ Python Worker  │   │  n8n Automator│
│   Core    │   │  (Dispatcher)  │   │  (External IO)│
│(STATE +  │   │                │   │               │
│  QUEUE)   │   └───────┬────────┘   └──────┬────────┘
└───────┬───┘           │                    │
        │               │                    │
        └───────────────┼────────────────────┘
                        │
            ┌───────────▼──────────┐
            │    Agent Runtime     │
            │  (OpenClaw + Ollama) │
            └──────────┬───────────┘
                       │
            ┌──────────▼────────┐
            │   pgvector RAG    │
            └───────────────────┘
```

---

## 🔥 COMPONENT ROLES (STRICT)

### 1. ✅ Postgres (SOURCE OF TRUTH)

**YOU MUST make Postgres your system brain**

Tables:

**task_queue**
```sql
- id
- task_type
- payload_json
- priority
- status (PENDING, RUNNING, DONE, FAILED)
- assigned_agent
- retry_count
- max_retries
- created_at
- updated_at
```

**agent_state**
```sql
- agent_name
- status (IDLE, BUSY, ERROR)
- current_task_id
- last_heartbeat
- capability_tags
```

**execution_log**
```sql
- id
- task_id
- agent
- input
- output
- error
- timestamp
```

**message_bus**
```sql
- id
- from_agent
- to_agent
- message_type
- payload
- status (UNREAD, READ)
- created_at
```

---

### 2. ✅ Python Dispatcher (THE REAL ORCHESTRATOR)

This is your **SOL-lite execution engine**

Responsibilities:
- Poll `task_queue`
- Choose agent based on:
  - availability
  - capability
  - priority
- Lock task (FOR UPDATE SKIP LOCKED)
- Dispatch execution
- Handle retries + backoff
- Update state machine
- Emit inter-agent messages

---

### 3. ✅ Agent Execution Layer (OpenClaw + Ollama)

Each agent:
- Receives structured task payload
- Executes
- Returns structured output

**NO orchestration logic inside agents**

Agents are:
> Stateless executors with memory access

---

### 4. ✅ n8n (LIMITED ROLE)

n8n becomes:
> **Peripheral automation, NOT orchestrator**

Use it for:
- Webhooks
- Scheduling triggers
- External APIs
- Notifications
- Email/SMS flows

n8n should: ✅ CREATE tasks ✅ UPDATE results
n8n should NOT: ❌ Coordinate agents ❌ Manage execution state

---

### 5. ❌ OpenClaw TaskFlow (DE-PRIORITIZED)

Use ONLY for:
- Small internal agent routines
- Tool chaining inside a single agent

Do NOT use for:
- multi-agent orchestration
- state tracking
- retries or failure recovery

---

## ⚙️ CORE SYSTEM DESIGN

### ✅ STATE MACHINE (MANDATORY)

```
PENDING → DISPATCHED → RUNNING
RUNNING → DONE
RUNNING → FAILED → RETRY → RUNNING
RUNNING → FAILED → DEAD
```

### ✅ DISPATCH LOGIC

```sql
SELECT * FROM task_queue
WHERE status = 'PENDING'
ORDER BY priority DESC
FOR UPDATE SKIP LOCKED
LIMIT 1;
```

Then:
1. Find available agent
2. Assign task
3. Update → RUNNING
4. Execute
5. Write logs
6. Handle success/failure

### ✅ FAILURE RECOVERY

Rules:
- Retry up to `max_retries`
- Exponential backoff
- Move to DEAD queue if exceeded
- Emit alert task

### ✅ INTER-AGENT MESSAGING

Use `message_bus` table

Pattern:
```
ATLAS → SOL → write message → CODY picks up
```

Polling or event-driven:
```sql
SELECT * FROM message_bus
WHERE to_agent='CODY' AND status='UNREAD'
```

---

## 🔁 TASK TYPES YOU WILL SUPPORT

Examples:
- `RESEARCH`
- `GENERATE_CODE`
- `VALIDATE_OUTPUT`
- `RISK_CHECK`
- `DEPLOY_WORKFLOW`
- `WRITE_COPY`

Each maps to:
> Agent capability + execution handler

---

## 📊 WHY OPTION C (WITH MODIFICATION) WINS

| Option | Verdict | Reason |
|--------|---------|--------|
| A) TaskFlow | ❌ | No state control, opaque |
| B) n8n Only | ❌ | No deterministic orchestration |
| C) Postgres Queue | ✅ CORE | Full control, scalable |
| D) Hybrid | ✅✅ BEST | Control + flexibility |

---

## 🚀 SIMPLIFIED VERSION (MVP BUILD ORDER)

### Phase 1 (YOU SHOULD BUILD FIRST)
- [ ] Postgres tables
- [ ] Python dispatcher
- [ ] Basic task execution
- [ ] Single agent loop

### Phase 2
- [ ] Multi-agent routing
- [ ] Retry system
- [ ] Logging

### Phase 3
- [ ] Message bus
- [ ] Agent coordination chains
- [ ] Learning loops

### Phase 4
- [ ] Optimization
- [ ] Priority scheduling
- [ ] Dynamic task generation

---

## 🧪 VALIDATION CHECK

Before calling it complete:
- [ ] Can system recover from crash?
- [ ] Can tasks retry + resume?
- [ ] Can agents communicate WITHOUT n8n?
- [ ] Can you replay execution logs?

---

## ✅ FINAL DECISION

> Build your own orchestration layer on Postgres + Python.
> Use n8n for edges.
> Keep OpenClaw lightweight inside agents.

---

## ✅ VALIDATION

- Execution path: PASS
- Authority boundaries: PASS
- SOL involvement: REQUIRED (execution layer)
- Risk coverage: PASS
- Completeness: PASS

---

## NEXT STEP

If you want next step, Copilot offered:
- ✅ Full Postgres schema (copy-paste)
- ✅ Python dispatcher (production-ready)
- ✅ Agent contract spec
- ✅ Task type registry

Say: **"BUILD CORE"** and I'll drop the full system.

---
**Source:** Microsoft 365 Copilot Chat (Basic)  
**Saved by:** Sol  
**Date:** 2026-06-09 06:25 CDT
