
## 2026-06-03T14:00:39.377756+00:00 — Release Check
- Checked: 10 releases
- Known: 10
- New since last: 1

### New Releases Detected
- **v2026.6.1-beta.3** (🧪 BETA) ⭐ INTERESTING — Published: 2026-06-03T09:16:02Z
  -  ### Highlights  - Agents and CLI-backed runtimes recover more cleanly from interrupted tool calls, stale session bindings, compaction handoffs, and media delivery retries. (#88129, #88136, #88141, #88162, #88182) - Channels and mobile delivery are steadier across Telegram, WhatsApp, iMessage, Slack, Discord, Microsoft Teams, Google Chat, Google Meet, and iOS realtime Talk. (#88096, #88105, #88183, #88231) - Provider and plugin requests now bound more timers, retries, OAuth/device-code lifetimes...

### 🌿 Green Summary — v2026.6.1-beta.3
- **Status:** Beta release
- **Published:** 2026-06-03T09:16:02Z
- **Matched Keywords:** memory, dreaming, dream, agent, workflow, performance, fix, MCP, context, embedding, vector, sync
- **Full Body:**
```

### Highlights

- Agents and CLI-backed runtimes recover more cleanly from interrupted tool calls, stale session bindings, compaction handoffs, and media delivery retries. (#88129, #88136, #88141, #88162, #88182)
- Channels and mobile delivery are steadier across Telegram, WhatsApp, iMessage, Slack, Discord, Microsoft Teams, Google Chat, Google Meet, and iOS realtime Talk. (#88096, #88105, #88183, #88231)
- Provider and plugin requests now bound more timers, retries, OAuth/device-code lifetimes, media downloads, local service probes, and generated-content polling paths before they can hang a run.
- Skills, session metadata, gateway runtime state, plugin metadata, memory watchers, and store writes do less repeated work on hot paths while keeping config, dispatch, and Linux file-watch behavior stable. (#89185, #89188, #85351) Thanks @RomneyDa and @NianJiuZst.
- Skills and plugin loading now handle stale disabled snapshots and loader failures more clearly, so channel turns avoid disabled SecretRefs and operators get better recovery guidance. (#79072, #79173) Thanks @zeus1959.
- Workboard, SecretRef plugin manifests, hosted iOS push relay, and external Copilot/Tokenjuice packaging add broader orchestration, integration, and plugin delivery surfaces. (#82326, #87469, #87796, #88107, #88117)
- Skill Workshop now has a fuller Control UI flow with proposal lists, today actions, revision handoff, searchable file previews, review states, locale coverage, and reusable session routing.
- Chat and Control UI startup paths keep sends alive through history loading, stream deltas incrementally, skip markdown work while streaming, keep drafts local while typing, clear the composer after sends, trace first-output latency, prioritize first connect, and expose calmer composer controls. (#88772, #88825, #88998, #89030, #89106) Thanks @vincentkoc and @sallyom.
- Provider coverage and model metadata now include MiniMax M3, account OAuth endpoints, Google/Vertex catalog fixes, OpenRouter 
```
- **Recommendation:** Monitor for stable promotion — beta with interesting features.
