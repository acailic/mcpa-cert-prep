# Free Practice Quiz — 20 original questions, exam-weighted

> Written for this pack (original — no exam content). Weights mirror the exam:
> D1×3, D2×3, D3×5, D4×5, D5×4. Target: **≥15/20**. Answers + explanations at the bottom.

---

**1. (D1)** What problem does MCP primarily solve?
A. Reducing LLM inference cost
B. Standardizing how AI applications connect to tools and data (M×N → M+N)
C. Replacing REST APIs for web applications
D. Improving model reasoning quality

**2. (D1)** Who controls when a **prompt** template is used?
A. The model decides automatically
B. The server pushes it on every request
C. The user explicitly selects it
D. The host rotates them on a schedule

**3. (D1)** A server needs an LLM completion. What is the correct MCP pattern?
A. Server calls the model provider API with its own key
B. Server sends `sampling/createMessage` to the client, which runs its configured model
C. Server embeds a local LLM runtime
D. Servers cannot access any LLM

**4. (D2)** How many servers may one MCP client connection serve?
A. Unlimited — clients multiplex
B. Exactly one
C. Two (primary + fallback)
D. One per transport

**5. (D2)** Immediately after the server answers the `initialize` request, what does the client send?
A. `tools/list`
B. `ping`
C. `notifications/initialized`
D. `resources/list`

**6. (D2)** Which transport fits a server launched as a local subprocess?
A. Streamable HTTP with OAuth
B. stdio
C. WebSocket
D. HTTP+SSE

**7. (D3)** A `tools/call` executes, but the operation failed (e.g. file not found). What does the client receive?
A. JSON-RPC error `-32603`
B. A successful response whose result has `isError: true`
C. `notifications/cancelled`
D. No response; the server retries

**8. (D3)** What does the client do upon receiving `notifications/tools/list_changed`?
A. Nothing — the list auto-updates
B. Disconnect and re-initialize
C. Re-call `tools/list` to refresh its cached list
D. Send `notifications/cancelled`

**9. (D3)** Which statement about tool **annotations** is correct?
A. `readOnlyHint: true` guarantees the tool cannot modify state
B. Annotations are advisory hints; clients must still enforce safety behavior
C. Annotations are required on every tool
D. Annotations replace input validation

**10. (D3)** During a long-running `tools/call` with a progress token, the user cancels. What does the client send?
A. `tools/cancel` request and waits for an ack
B. `notifications/cancelled {requestId}` — a notification with no reply
C. JSON-RPC error `-32600`
D. `ping` to abort

**11. (D3)** A server not paginating correctly hands the client a cursor. What may the client do with it?
A. Parse it to jump to page 3
B. Cache it permanently across sessions
C. Pass it back verbatim in the next request
D. Decompress it into a page index

**12. (D3)** Which method does a server use to get structured input from the **user** mid-flow? (🟡 newer spec)
A. `sampling/createMessage`
B. `elicitation/create`
C. `roots/list`
D. `completion/complete`

**13. (D4)** An MCP server receives the client's API token and forwards it unchanged to a third-party API. This pattern is:
A. Acceptable if the token is short-lived
B. Token passthrough — forbidden; the server must validate audience and act as its own resource server
C. Fine over HTTPS only
D. Required for federation

**14. (D4)** Why is PKCE mandatory in MCP's OAuth 2.1 profile?
A. It encrypts tool payloads
B. It protects the authorization code from interception, especially for public clients
C. It replaces the need for client secrets everywhere
D. It scopes tokens to filesystem roots

**15. (D4)** A published tool's description quietly changes after the user approved it, now instructing the model to exfiltrate data. This is:
A. A rug-pull / tool-poisoning pattern; mitigate with change detection and re-consent
B. Normal versioning; no action needed
C. A confused deputy attack on the transport
D. Session hijacking

**16. (D4)** Malicious instructions are embedded inside a tool's *result text*, trying to make the agent call another tool. The defense is:
A. Trust results only from approved servers
B. Treat tool results as untrusted data; don't auto-execute instructions found in them
C. Use Streamable HTTP instead of stdio
D. Add more tool annotations

**17. (D4)** Where should OAuth access tokens be sent?
A. In the query string for cacheability
B. In the `Authorization` header, bound to the resource's audience
C. In the JSON-RPC `params._meta`
D. In the `Mcp-Session-Id` header

**18. (D5)** An enterprise wants teams to adopt MCP without leaking source code. Which combination fits best?
A. Allow every server; rely on model alignment
B. Approved-server allowlist, scoped credentials, roots-based boundaries, audit logging
C. Disable tools; resources only
D. One shared admin token for all servers

**19. (D5)** A new MCP-compatible host ships tomorrow. What happens to an existing conforming server?
A. It must be rewritten per host
B. It works unchanged — protocol-level portability is MCP's value proposition
C. It needs a vendor-specific plugin
D. It must downgrade its transport to stdio

**20. (D5)** Which is the WEAKEST reason to introduce MCP in a project?
A. Multiple AI hosts will consume the same tools
B. Vendor-neutral integration across model providers is required
C. A single app needs one fixed internal API call with no LLM involvement
D. Teams need governed, auditable access to enterprise data via agents

---

## Answers

| # | Ans | Why |
|---|---|---|
| 1 | B | MCP standardizes AI-app↔tool/data connectivity: M×N integrations become M+N. |
| 2 | C | Prompts are **user-controlled** (tools=model, resources=application). |
| 3 | B | Sampling is delegated: server requests, **client's** model executes, user approves. |
| 4 | B | 1 client = 1 server; the host fans out to many clients. |
| 5 | C | Handshake completes with `notifications/initialized` before any normal request. |
| 6 | B | Local subprocess → stdio (trusted machine boundary). |
| 7 | B | Business failure = success envelope with `isError: true`; `-32603` is for internal protocol errors. |
| 8 | C | `listChanged` invalidates the cache; re-list (and re-consent where required). |
| 9 | B | Annotations are hints, not enforcement. |
| 10 | B | Cancellation is a notification: no ack, no error; late results discarded. |
| 11 | C | Cursors are opaque — return verbatim. |
| 12 | B | Elicitation asks the user; sampling asks the model. |
| 13 | B | Token passthrough is forbidden; bind tokens by audience, act as resource server. |
| 14 | B | PKCE stops authorization-code interception (public clients). |
| 15 | A | Post-approval tool change = rug pull; re-consent on changes. |
| 16 | B | Tool output is untrusted data — the core prompt-injection defense. |
| 17 | B | Header + audience binding; never URLs (logged) and never RPC params. |
| 18 | B | Classic governance stack: allowlist + least privilege + boundaries + audit. |
| 19 | B | Conformance = portability; any host, unchanged server. |
| 20 | C | No LLM involvement and a single fixed integration = no MCP benefit. |
