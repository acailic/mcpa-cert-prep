# Flashcards — rapid Q→A

## Lifecycle (stateless — 2026-07-28)
Q: Is there a handshake in current MCP? → A: **No.** `initialize`/`notifications/initialized` were removed; every request carries `_meta` protocolVersion + clientCapabilities.
Q: How does a client learn a server's versions/capabilities up front? → A: `server/discover` (servers MUST implement).
Q: What happens on protocol version mismatch? → A: `UnsupportedProtocolVersionError` (spec code `-32022`).
Q: What did the 2026-07-28 rewrite remove? → A: sessions/`Mcp-Session-Id`, `initialize` handshake, `ping`, `logging/setLevel`, batching, stream resumability.
Q: What did it deprecate? → A: Roots, Sampling, Logging features; HTTP+SSE transport; OAuth DCR (≥12-month window).

## Control ownership
Q: Tools are controlled by ___? → A: The model (LLM decides when to call).
Q: Resources are controlled by ___? → A: The application (host attaches context).
Q: Prompts are controlled by ___? → A: The user (explicitly selected).
Q: Who executes a sampling request? → A: The client, with its configured LLM, after user approval.

## Message basics
Q: Notification vs request? → A: Notification has no `id`, expects no reply; request has `id`, must get a response.
Q: Can a JSON-RPC response contain result AND error? → A: No — result XOR error.
Q: `-32601`? → A: Method not found. (`-32602` invalid params, `-32603` internal, `-32700` parse, `-32600` invalid request.)
Q: Tool failed at runtime (file not found). Error object or…? → A: Successful response with `isError: true` + message in content.

## Transports
Q: Two current transports? → A: stdio (local) and Streamable HTTP (remote). HTTP+SSE is deprecated.
Q: Streamable HTTP = ? → A: HTTP POST client→server + optional SSE stream server→client; standard headers `Mcp-Method`/`Mcp-Name`; **no sessions, no resumability** — re-issue with new request ID.

## Tools / Resources / Prompts
Q: What validates tool arguments? → A: The tool's declared JSON Schema `inputSchema`.
Q: Name the four tool annotation hints. → A: `readOnlyHint`, `destructiveHint`, `idempotentHint`, `openWorldHint`.
Q: Are annotations enforceable? → A: No — advisory hints only.
Q: Resource content types? → A: `text` or `blob` (base64) with `mimeType`.
Q: Resource subscriptions flow (2026-07-28)? → A: `subscriptions/listen` (one long-lived stream; opt-in types) → tagged events → client re-reads. Old `resources/subscribe` = removed.
Q: What is `completion/complete` for? → A: Autocompleting prompt/resource-template arguments.
Q: Pagination cursor rules? → A: Opaque — pass back verbatim, never parse or construct.

## Server→client primitives (now MRTR)
Q: Three things clients expose TO servers? → A: Sampling, roots, elicitation handling — all **deprecated features** in 2026-07-28, delivery via MRTR.
Q: MRTR flow? → A: Server returns `InputRequiredResult` (`resultType: "input_required"`, `inputRequests`) → client retries same request with `inputResponses`.
Q: Do servers still send standalone s→c requests (`sampling/createMessage`, `roots/list`, `elicitation/create`)? → A: **No** — replaced by MRTR; those method names are legacy distractors.
Q: Sampling flow (concept)? → A: Server asks → user approves → client's model runs → completion returned. Client enforces modelPreferences.
Q: Roots are…? → A: Client-declared URI boundaries; advisory scoping, not sandboxing. Deprecated.
Q: Elicitation asks ___ ; sampling asks ___ . → A: the user; the model.
Q: Tasks live where since 2026-07-28? → A: Official extension `io.modelcontextprotocol/tasks` — poll `tasks/get`, input via `tasks/update`, no `tasks/list`, no blocking `tasks/result`.

## Security
Q: Token passthrough? → A: Forbidden. Validate audience; server is its own OAuth resource server.
Q: MCP OAuth profile in 3 words? → A: OAuth 2.1 + mandatory PKCE.
Q: RFC 9728? → A: Protected Resource Metadata — well-known doc naming the authorization server(s).
Q: RFC 8707? → A: Resource indicators — `aud`-bind tokens to the specific server.
Q: Where do tokens never go? → A: URLs/query strings (they get logged); header only.
Q: Session ID rules? → A: Protocol sessions removed (2026-07-28); harden any server-minted handles the same way: secure-random, origin/auth-validated, expire.
Q: Rug pull? → A: Tool changed after approval → detect `listChanged`, re-consent.
Q: Line-jumping? → A: Prompt injection inside tool results → treat results as untrusted data.
Q: Confused deputy? → A: Authorized component tricked into misusing its authority → per-audience consent/binding.
Q: stdio trust boundary? → A: The local machine; stdio servers shouldn't do web-token dance.

## Ecosystem
Q: MCP's core value in one line? → A: Write a conforming server once, any MCP host can use it (M×N → M+N).
Q: Exam domain weights? → A: D1 16, D2 14, D3 26, D4 24, D5 20.
Q: Spec version the exam aligns to? → A: Release 2026-07-28 (🟡 re-diff before exam day).
Q: Cert logistics? → A: Online proctored MCQ, 90 min (🟡120), valid 2 years, 12-mo window, 1 retake, money-back.
