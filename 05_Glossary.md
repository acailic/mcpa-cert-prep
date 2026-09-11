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

**Sampling** — server→client request to run an LLM completion with the *client's* model, with user approval.

**Elicitation** — server→client request for structured user input mid-flow (schema-constrained form). 🟡

**Roots** — client-declared URI boundaries (e.g. a project dir) advising the server where to operate;
advisory, not enforcement.

**Capability (negotiation)** — declared support flags exchanged in `initialize`; gates which methods may
be used (e.g. `tools.listChanged`).

**initialize handshake** — client proposes protocolVersion+capabilities → server replies → client sends
`notifications/initialized`.

**protocolVersion** — date-string spec version (e.g. `2025-06-18`); negotiated, not assumed.

**JSON-RPC 2.0** — message format: requests (id), responses (result XOR error), notifications (no id).

**Notification** — id-less message; no response (`notifications/cancelled`, `/progress`, `list_changed`).

**Transport** — wire layer: **stdio** (local subprocess) or **Streamable HTTP** (remote; HTTP+SSE is
deprecated).

**Streamable HTTP** — POST for client→server + optional SSE stream for server→client; session resumption
via session ID.

**Mcp-Session-Id** — session identifier header for Streamable HTTP; must be validated/rotated.

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

**Token passthrough** — forbidden pattern: forwarding a client token verbatim to another API.

**Confused deputy** — attack where an authorized component is tricked into misusing its authority.

**Tool poisoning / rug pull** — malicious/changed tool metadata or behavior after approval; mitigate with
change detection + re-consent.

**Line-jumping / prompt injection via tool results** — untrusted instructions embedded in tool output.

**Registry** — directory of MCP servers (official registry preview 🟡); enterprises use allowlists.

**MCPA** — Model Context Protocol Associate; LF's vendor-neutral MCP certification (this exam).
