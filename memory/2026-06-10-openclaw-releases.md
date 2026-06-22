
## 2026-06-10T14:00:35.827567+00:00 — Release Check
- Checked: 10 releases
- Known: 10
- New since last: 1

### New Releases Detected
- **v2026.6.5** (🚀 STABLE) ⭐ INTERESTING — Published: 2026-06-09T18:13:20Z
  -  ### Highlights  - QQBot now strips model reasoning/thinking scaffolding before native delivery, preventing raw `<thinking>` content from leaking into channel replies. (#89913, #90132) Thanks @openperf. - MCP tool results now coerce `resource_link`, `resource`, `audio`, malformed image, and future non-text/image blocks at the materialize boundary, preventing Anthropic 400s and poisoned session history after a tool returns richer MCP content. (#90710, #90728) Thanks @RanSHammer and @849261680. - ...

### 🌿 Green Summary — v2026.6.5
- **Status:** Stable release
- **Published:** 2026-06-09T18:13:20Z
- **Matched Keywords:** memory, agent, fix, MCP, context, sync
- **Full Body:**
```

### Highlights

- QQBot now strips model reasoning/thinking scaffolding before native delivery, preventing raw `<thinking>` content from leaking into channel replies. (#89913, #90132) Thanks @openperf.
- MCP tool results now coerce `resource_link`, `resource`, `audio`, malformed image, and future non-text/image blocks at the materialize boundary, preventing Anthropic 400s and poisoned session history after a tool returns richer MCP content. (#90710, #90728) Thanks @RanSHammer and @849261680.
- Anthropic extended-thinking sessions recover after prompt-cache expiry or Gateway restart because stream start events wait for `message_start`, letting pre-generation signature errors trigger the existing recovery retry. (#90667, #90697) Thanks @openperf.
- Parallel is now a bundled `web_search` provider with `PARALLEL_API_KEY` discovery, guarded endpoint handling, cache-safe session ids, onboarding picker support, and docs. (#85158) Thanks @NormallyGaussian.
- Google Vertex ADC users get static catalog rows and runtime model resolution again, while single-provider cooldown recovery and memory adapter status checks are more reliable. (#90506, #90609, #90717, #90816) Thanks @849261680.
- Matrix can preflight voice notes before mention gating, preserve thread reads/replies through Matrix relations pagination, and carry QA coverage for voice and thread flows. (#78016, #90415)
- Auth and plugin install state is more durable: auth profiles now live in SQLite, official npm plugin install records keep their trusted pins, and prerelease fallback integrity checks avoid carrying stale integrity forward. (#89102, #88585)
- Agent, tool, and provider loops are stricter around MCP lease timestamps, prompt-cache tool names, local tool catalogs, unreadable dynamic tools, owner-only HTTP tools, and provider catalog metadata, reducing hidden retries and unsafe exposure. (#91124, #91233, #90022, #90261)
- macOS node mode no longer silently self-reconnects away from a healthy direct Gateway sess
```
- **Recommendation:** Consider updating — stable release with relevant improvements.
