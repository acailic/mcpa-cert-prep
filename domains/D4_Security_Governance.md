# D4 — Security & Governance (24% — second biggest)

Sources: MCP spec "Security Best Practices" + authorization spec sections. 24% of the exam — know the
named attacks and their named mitigations.

## Trust model (✅)

- **Explicit user consent** for data access and tool execution; clients SHOULD surface clearly what is
  being shared/approved.
- Servers are **untrusted by default**; the client mediates every capability (sampling approval,
  elicitation forms, roots disclosure).
- Data privacy: user data goes only where the user agreed; no blanket telemetry.

## The named attack patterns (✅ — memorize list + mitigation)

| Attack | What it is | Mitigation |
|---|---|---|
| **Confused deputy** | a legitimately-authorized component is tricked into misusing its authority (e.g. client's OAuth creds used against a third party) | consent per audience; bind tokens to audience/resource; don't proxy blindly |
| **Token passthrough** | server accepts a token from client and forwards it to another API verbatim | **forbidden**: server must be its own OAuth resource server; validate audience (`aud`) per RFC 8707 |
| **Session hijacking / replay** | attacker steals/replays session or stream IDs | **2026-07-28: protocol sessions are removed** (no `Mcp-Session-Id`). Legacy guidance (secure-random, origin-validated, rotate/expire) still applies to any server-minted *handles* and stream state; the exam may phrase this attack against the old session model — recognize it either way. |
| **Tool poisoning / rug pull** | tool definitions change after approval (description hides injection, or list swap) | re-list + **re-consent on tool changes**; `listChanged` handling; pin/review manifests |
| **Shadow tools / line-jumping** | malicious instructions *inside tool results* hijack the model (prompt injection) | treat tool output as untrusted data; sandbox; user visibility; don't auto-execute |
| **Credential leakage in URLs** | tokens in query strings end up in logs | tokens only in `Authorization` header |
| **Cross-server confusion** | one server's data/actions attributed to another | clear server identity, per-server consent scoping |

## Authorization architecture (✅ — HTTP transport)

- MCP servers are **OAuth 2.1 resource servers**:
  - **PKCE is mandatory** for all clients (public clients especially).
  - **Protected Resource Metadata** (RFC 9728): server publishes `/.well-known/oauth-protected-resource`
    → tells clients which authorization servers to use.
  - **Resource Indicators** (RFC 8707): tokens bound to `aud` = this server; prevents token confusion
    across services.
  - **Client registration** (2026-07-28): **Client ID Metadata Documents** are the preferred mechanism;
    OAuth 2.0 **Dynamic Client Registration (RFC 7591) is deprecated** but kept for backward compat.
    Clients MUST key credentials by issuer, never reuse them across authorization servers, and MUST
    validate a present `iss` (RFC 9207) before redeeming codes. Confidential clients keep secrets
    server-side only.
- **Token storage**: never in URLs; prefer OS keychain/secret store; short-lived access tokens,
  refresh handled by the client.
- **Local (stdio) servers**: trust boundary is the machine — avoid handling raw third-party tokens;
  don't put internet-facing auth on stdio servers.

## Human-in-the-loop governance (✅)

- Approval gates: tool calls (esp. destructive), sampling requests, elicitation forms, root disclosure.
- `readOnlyHint`/`destructiveHint` annotations help clients decide UI treatment — but the **client must
  enforce** (hints are advisory).
- Progress + logging visibility: users should see what a server is doing (governance = observability too).

## Auditability & operations (✅/🟡)

- Log: tool calls (who/what/params/when), consent events, auth events, capability changes — tamper-evident
  where regulation requires (enterprise deployments).
- Rate limiting + timeouts at client/host level protect against runaway servers.
- Enterprise governance: data boundaries, approved-server lists (registry allowlists — official Registry
  still in preview), DLP scanning of tool payloads, incident response = revoke access + rotate tokens.

## Safety controls checklist (exam-ready list)

1. Per-action consent, esp. writes/destructive ops
2. Audience-bound OAuth 2.1 + PKCE, no passthrough
3. Validate any session/stream/handle state per request (protocol sessions themselves are gone — 2026-07-28)
4. Tool-list change detection → re-consent
5. Treat tool *results* as untrusted (prompt-injection defense)
6. Sandboxing + least-privilege credentials (root scoping legacy — roots feature deprecated, boundaries now via tool params/config)
7. Audit logging of calls + consents; retention policy
8. Supply-chain: install servers from trusted sources; verify provenance/signatures 🟡

## Exam angles (D4)

- "Server forwards the client's GitHub token to another API" → **token passthrough, forbidden**; audience
  binding.
- "Tool description changed after approval" → re-consent on change (rug pull).
- "Malicious instruction embedded in a tool result" → prompt injection / line-jumping; treat as data.
- "Why PKCE?" → protects authorization-code interception for public clients.
- "Where do tokens belong?" → `Authorization` header, never query strings, audience-bound.
- Governance question format: *policy → control* mapping (consent, audit, least privilege).
