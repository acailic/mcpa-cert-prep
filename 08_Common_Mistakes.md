# Common Mistakes — traps that lose points

1. **Business failure ≠ protocol error.** A tool that "ran but failed" returns a *successful* response
   with `isError: true`. Only protocol violations (unknown method, bad params) produce JSON-RPC `error`.
2. **Talking before `notifications/initialized`.** `initialize` response alone doesn't open traffic —
   the client must send the initialized notification first.
3. **Calling non-negotiated capabilities.** If the server's initialize response lacks `tools`,
   `tools/list` is a protocol violation → method-not-found.
4. **1:1 client↔server confusion.** One client connects to ONE server; the *host* aggregates many.
   Servers never talk to servers.
5. **Annotations treated as enforcement.** `readOnlyHint`/`destructiveHint` are advisory hints — clients
   (and you) must not claim they guarantee behavior.
6. **Roots treated as sandboxing.** Roots scope where a server works; they are advisory boundaries, not
   a security container.
7. **Sampling misunderstood as server-side LLM calls.** The *client* executes sampling with the user's
   model, after approval; servers never call models directly.
8. **Cancel expecting a response.** `notifications/cancelled` is a notification — no reply, no error;
   late results are simply discarded.
9. **Parsing cursors.** Pagination cursors are opaque — pass them back verbatim; constructing your own
   is invalid.
10. **Forgetting re-list after `listChanged`.** `tools/listChanged` means the cached list is stale —
    re-call `tools/list` (and re-consent users where required).
11. **Token passthrough "works so it's fine".** Forwarding client tokens to another API is explicitly
    forbidden; servers must be their own resource server with audience validation.
12. **Tokens in query strings.** Tokens belong in the `Authorization` header; URLs get logged.
13. **PKCE optional.** In OAuth 2.1 PKCE is required for all clients, not just SPAs.
14. **Session IDs treated as random-but-unvalidated.** They must be secure-random AND validated per
    request (origin/auth binding) to stop hijacking/replay.
15. **HTTP+SSE assumed current.** Deprecated → replaced by Streamable HTTP.
16. **stdios get internet auth.** Local stdio servers sit inside the machine's trust boundary; bolting
    web OAuth onto them is a smell (and vice versa: remote servers without auth are worse).
17. **MCP as a general web-API replacement.** It's a control/context plane for AI apps — not the answer
    to "replace REST everywhere".
18. **Wrong control owner.** tools=model, resources=application, prompts=user. Mix these up and every
    "who initiates X" question dies.
19. **protocolVersion assumed latest.** The *negotiated* version governs; clients must handle servers
    answering with an older supported date.
20. **Elicitation vs sampling mixed.** Sampling asks the model; elicitation asks the *user*; both are
    client-mediated and both require client support.
