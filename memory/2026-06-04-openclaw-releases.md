
## 2026-06-04T14:00:32.798971+00:00 — Release Check
- Checked: 10 releases
- Known: 10
- New since last: 2

### New Releases Detected
- **v2026.6.2-beta.1** (🧪 BETA) ⭐ INTERESTING — Published: 2026-06-03T23:46:41Z
  - ## 2026.6.2  ### Highlights  - Plugin and skill installs now use an operator install policy instead of the old dangerous-code scanner path, with clearer doctor, CLI, ClawHub, and troubleshooting surfaces for package, archive, source, upload, and marketplace installs. (#89516) Thanks @joshavant. - Telegram, Feishu, Discord, WhatsApp, and outbound delivery paths got safer around duplicate transcript mirrors, Telegram admin writeback, streamed-final previews, approval allowlists, setup runtime stat...
- **v2026.6.1** (🚀 STABLE) ⭐ INTERESTING — Published: 2026-06-03T19:35:12Z
  -  ### Highlights  - Agents and CLI-backed runtimes recover more cleanly from interrupted tool calls, stale session bindings, compaction handoffs, and media delivery retries. (#88129, #88136, #88141, #88162, #88182) - Channels and mobile delivery are steadier across Telegram, WhatsApp, iMessage, Slack, Discord, Microsoft Teams, Google Chat, Google Meet, and iOS realtime Talk. (#88096, #88105, #88183, #88231) - Provider and plugin requests now bound more timers, retries, OAuth/device-code lifetimes...

### 🌿 Green Summary — v2026.6.2-beta.1
- **Status:** Beta release
- **Published:** 2026-06-03T23:46:41Z
- **Matched Keywords:** memory, agent, workflow, performance, fix, context
- **Full Body:**
```
## 2026.6.2

### Highlights

- Plugin and skill installs now use an operator install policy instead of the old dangerous-code scanner path, with clearer doctor, CLI, ClawHub, and troubleshooting surfaces for package, archive, source, upload, and marketplace installs. (#89516) Thanks @joshavant.
- Telegram, Feishu, Discord, WhatsApp, and outbound delivery paths got safer around duplicate transcript mirrors, Telegram admin writeback, streamed-final previews, approval allowlists, setup runtime state, poll modifiers, Discord voice errors, and internal progress traces. (#88973, #89626, #89812, #89035, #89814, #89813, #89601) Thanks @pgondhi987, @Petru2224, @zhangguiping-xydt, @codezz, and @takhoffman.
- Chat, Control UI, Skill Workshop, Workboard, Android companion shell, and WebChat flows now preserve visible streaming text, reconcile completed sends, expose ACK timing, add Workboard keyboard movement, harden dialog accessibility, lazy-load usage views, keep current chat toggles working, and improve Android companion-first shell navigation. (#89801, #89777, #89802) Thanks @vincentkoc.
- Security, policy, and config recovery now reject corrupt shell snapshots, unsupported policy keys, unsafe exec approval precheck environments, malformed script limits, and suspicious gateway startup configs while adding data-handling conformance checks. (#89701, #87074, #81488, #87056, #89480) Thanks @RomneyDa, @giodl73-repo, and @mmaps.
- Gateway, agent, Codex, provider, model, and memory paths now recover session write-lock release failures, abandoned Codex app-server startups, stream-to-parent ACP spawns, custom-provider runtime fanout, bundled provider aliases, prompt-cache boundaries, Gemini stop sequences, Kimi cache markers, and watcher pressure warnings. (#89811, #89244) Thanks @RomneyDa and @takhoffman.
- Release, CI, Docker, Crabbox/Testbox, package, and E2E validation lanes now bound more network calls, malformed numeric limits, process groups, cleanup leaks, package hydration
```
- **Recommendation:** Monitor for stable promotion — beta with interesting features.

### 🌿 Green Summary — v2026.6.1
- **Status:** Stable release
- **Published:** 2026-06-03T19:35:12Z
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
- **Recommendation:** Consider updating — stable release with relevant improvements.
