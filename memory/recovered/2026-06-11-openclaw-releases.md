
## 2026-06-11T14:00:35.281424+00:00 — Release Check
- Checked: 10 releases
- Known: 10
- New since last: 1

### New Releases Detected
- **v2026.6.6-beta.1** (🧪 BETA) ⭐ INTERESTING — Published: 2026-06-10T19:33:39Z
  - ## 2026.6.6  ### Highlights  - Security boundaries are substantially tighter across transcripts, sandbox binds, host environment inheritance, MCP stdio, Codex HTTP access, native search policy, elevated sender checks, deleted-agent ACP bypasses, loopback tools, Discord moderation, and Teams group actions; exec approvals now fail closed on timeout. (#91529, #91618, #91615, #91619, #91741, #91745, #91746, #91748, #91749, #91750, #91751, #91752, #91763, #89938) Thanks @joshavant, @pgondhi987, @mmap...

### 🌿 Green Summary — v2026.6.6-beta.1
- **Status:** Beta release
- **Published:** 2026-06-10T19:33:39Z
- **Matched Keywords:** memory, agent, performance, fix, MCP, context, embedding, sync
- **Full Body:**
```
## 2026.6.6

### Highlights

- Security boundaries are substantially tighter across transcripts, sandbox binds, host environment inheritance, MCP stdio, Codex HTTP access, native search policy, elevated sender checks, deleted-agent ACP bypasses, loopback tools, Discord moderation, and Teams group actions; exec approvals now fail closed on timeout. (#91529, #91618, #91615, #91619, #91741, #91745, #91746, #91748, #91749, #91750, #91751, #91752, #91763, #89938) Thanks @joshavant, @pgondhi987, @mmaps, @eleqtrizit, @shakkernerd, and @drobison00.
- Telegram delivery is safer and more coherent: account-scoped topics route to the right agent, streamed text survives tool calls, `/compact` works on generic ingress, callback handling uses concrete APIs, draft chunking is shared, durable dispatch dedupe moved into the SDK, and unauthorized DM text stays out of cache and prompt context. (#91189, #88682, #89588, #90212, #91876, #91874, #91904, #91478, #91915) Thanks @codysai001, @alexzhu0, @joelnishanth, @snowzlm, @obviyus, and @sallyom.
- iMessage recovery and delivery now cover always-on inbound restart, durable echo markers, block streaming, idle approval discovery, hardened outbound transport, and actionable inbound startup diagnostics. (#91335, #91449, #88969, #88530, #91783, #91785) Thanks @omarshahine, @jmissig, and @colmbrogan.
- Browser and MCP connectivity gained existing-session CDP support, discovered WebSocket validation, default-profile `cdpUrl` handling, safer browser-output boundaries, Streamable HTTP loopback transport, corrected OAuth/SSE authorization handling, and broader schema compatibility. (#91422, #89851, #91736, #91747, #91451, #80143) Thanks @pgondhi987, @anagnorisis2peripeteia, @lifuyue, @eleqtrizit, @LiuwqGit, and @HemantSudarshan.
- Control UI startup and first-reply latency are lower through cached model metadata, removal of the startup catalog wait, lazy slash-command loading, and first-event tracing with slow-reply diagnostics. (#91531, #91538, #9
```
- **Recommendation:** Monitor for stable promotion — beta with interesting features.
