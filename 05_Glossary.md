# Glossary (exam vocabulary)

**MCP (Model Context Protocol)** — open, JSON-RPC 2.0-based protocol standardizing how AI applications
connect to tools, data, and prompts.

**Host** — the AI application (IDE, desktop assistant, agent platform) that embeds clients and aggregates
servers; owns UX, consent, and aggregation.

**Client** — a connector inside a host holding exactly one connection to one server; mediates all traffic.

**Server** — a program exposing capabilities (tools/resources/prompts) over a transport.

**Tool** — a model-invoked action with a JSON Schema `inputSchema`; results may set `isError: true`.

**Resource** — application-controlled, read-only context identified by a URI; text or blob with mimeType.

**Prompt** — user-selected template returning a message array; supports arguments and embedded resources.

**Sampling** — client-run LLM completion on the server's behalf (client's model, user approval).
**Deprecated 2026-07-28**; delivery now via MRTR, not a standalone server→client request.

**Elicitation** — structured input requested from the *user* mid-flow (schema-constrained). Since
2026-07-28 rides inside MRTR `inputRequests` (no standalone `elicitation/create`).

**Roots** — client-declared URI boundaries (e.g. a project dir) advising the server where to operate;
advisory, not enforcement. **Deprecated 2026-07-28** (migrate to tool params/resource URIs/config).

**MRTR (Multi Round-Trip Requests)** — 2026-07-28 pattern replacing server-initiated requests: server
returns `InputRequiredResult` (`resultType: "input_required"`, `inputRequests` array); client answers
with `inputResponses` **on a retry of the original request**.

**server/discover** — server-side RPC (MUST implement) advertising supported protocol versions,
capabilities, identity; optional up-front version selection / stdio compat probe.

**subscriptions/listen** — single long-lived POST stream (2026-07-28) replacing the HTTP GET endpoint
and `resources/subscribe`/`unsubscribe`; clients opt in per notification type; events tagged
`io.modelcontextprotocol/subscriptionId`.

**Tasks extension** — `io.modelcontextprotocol/tasks` official extension (moved out of core 2026-07-28):
`tasks/get` polling, `tasks/update` client input, `tasks/cancel`, `notifications/tasks`; no `tasks/list`.

**Capability (declaration)** — per-side support flags (tools, resources, prompts, `extensions`…);
2026-07-28: carried per-request in `_meta`, no handshake. Gates which methods may be used.

**initialize handshake** — *(removed 2026-07-28)* client proposes protocolVersion+capabilities → server
replies → client sends `notifications/initialized`. Pre-rewrite concept — appears only as a distractor.

**protocolVersion** — date-string spec version (e.g. `2026-07-28`); since 2026-07-28 sent per-request in
`_meta` (`io.modelcontextprotocol/protocolVersion`); mismatch → `UnsupportedProtocolVersionError`.

**resultType** — required field on every result: `"complete"` or `"input_required"` (MRTR interim);
missing field (older servers) treated as `"complete"`.

**JSON-RPC 2.0** — message format: requests (id), responses (result XOR error), notifications (no id).
**No batching** (removed 2025-06-18).

**Notification** — id-less message; no response (`notifications/cancelled`, `/progress`, `list_changed`).

**Transport** — wire layer: **stdio** (local subprocess) or **Streamable HTTP** (remote; HTTP+SSE is
deprecated).

**Streamable HTTP** — POST for client→server + optional SSE stream for server→client; standard headers
`Mcp-Method`/`Mcp-Name`; **no sessions, no resumability** (broken stream → re-issue request).

**Mcp-Session-Id** — *(removed 2026-07-28)* former session header for Streamable HTTP. Distractor only.

**inputSchema** — JSON Schema describing a tool's arguments; clients validate/render from it.

**Tool annotations** — advisory hints: `readOnlyHint`, `destructiveHint`, `idempotentHint`, `openWorldHint`.

**`isError`** — tool-result flag marking business failure inside a successful protocol response.

**Progress token** — `_meta.progressToken` on requests; server reports via `notifications/progress`.

**Cancellation** — `notifications/cancelled {requestId}`; best-effort stop; late results discarded.

**Cursor** — opaque pagination token; pass back unmodified.

**OAuth 2.1** — authorization framework; PKCE mandatory; MCP servers act as resource servers.

**Protected Resource Metadata (RFC 9728)** — well-known document telling clients which authorization
servers serve this MCP server.

**Resource Indicators (RFC 8707)** — `aud`-binding of tokens to a specific resource/server.

**Dynamic Client Registration (RFC 7591)** — clients register themselves with the authorization server.
**Deprecated 2026-07-28** in favor of **Client ID Metadata Documents** (kept for backward compat).

**Token passthrough** — forbidden pattern: forwarding a client token verbatim to another API.

**Confused deputy** — attack where an authorized component is tricked into misusing its authority.

**Tool poisoning / rug pull** — malicious/changed tool metadata or behavior after approval; mitigate with
change detection + re-consent.

**Line-jumping / prompt injection via tool results** — untrusted instructions embedded in tool output.

**Registry** — directory of MCP servers; the official registry (registry.modelcontextprotocol.io) is
**still in preview**, run by the modelcontextprotocol project (not AAIF); enterprises use allowlists.

**MCPA** — Model Context Protocol Associate; LF's vendor-neutral MCP certification (this exam).
