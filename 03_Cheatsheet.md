# MCPA Cheatsheet — cram page

## 2026-07-28 rewrite in one breath (memorize)
**Stateless. No `initialize`, no `notifications/initialized`, no sessions/`Mcp-Session-Id`, no `ping`,
no `logging/setLevel`, no batching.** Every request carries `_meta` version + capabilities
(`io.modelcontextprotocol/protocolVersion`, `clientCapabilities`); every result carries
**`resultType`** (`"complete"` | `"input_required"`). `server/discover` = up-front version/capability
probe. Server→client requests are gone — replaced by **MRTR**. Deprecations: **roots, sampling,
logging, HTTP+SSE, OAuth DCR** (≥12-month window; still spec'd, don't adopt in new builds).

## Lifecycle order (memorize)
(optional) `server/discover` → normal traffic (`tools/list` → `tools/call` …). No handshake, no
initialized notification — an answer sequence containing either describes the pre-rewrite protocol.
Never call outside declared capabilities.

## Who controls what
| Primitive | Control |
|---|---|
| tools | model |
| resources | application (host) |
| prompts | user |
| sampling ⚠️ dep. | server requests → **client's LLM** runs it (user approves); via MRTR now |
| roots ⚠️ dep. | client defines boundaries (advisory) |
| elicitation | server asks **user** for structured input — now inside MRTR `inputRequests` |

## MRTR pattern (new — high-yield)
Server needs info mid-request → returns `InputRequiredResult` (`resultType: "input_required"`,
**`inputRequests`** array) → client collects (may ask user) → **retries the same request** with
**`inputResponses`**. Replaces standalone `sampling/createMessage`, `roots/list`, `elicitation/create`.

## Message types (JSON-RPC 2.0)
- Request: has `id`, must get a response.
- Response: `result` **XOR** `error`; result includes `resultType`.
- Notification: no `id`, no reply (`/cancelled`, `/progress`, `/message` (only if requested),
  `list_changed` events via `subscriptions/listen`).

## Methods table (current protocol)
| Method | Direction | Notes |
|---|---|---|
| `server/discover` | c→s | versions, capabilities, identity; MUST implement |
| `tools/list` `tools/call` | c→s | deterministic order; `ttlMs`+`cacheScope`; annotations = hints |
| `resources/list` `resources/read` | c→s | URI + templates; text/blob + mimeType |
| `subscriptions/listen` | c→s | replaces GET endpoint + `resources/subscribe`; opt-in types; `subscriptionId` tags |
| `prompts/list` `prompts/get` | c→s | returns messages array |
| `completion/complete` | c→s | argument autocomplete |
| `tasks/get` `tasks/update` `tasks/cancel` | c→s | **extension** `io.modelcontextprotocol/tasks`; poll, don't block |
| ~~`initialize`~~ ~~`ping`~~ ~~`logging/setLevel`~~ | — | **removed** in 2026-07-28 — distractors |
| ~~`sampling/createMessage`~~ ~~`roots/list`~~ ~~`elicitation/create`~~ | ~~s→c~~ | legacy forms — now MRTR; features sampling/roots deprecated |

## Error codes
`-32700` parse · `-32600` invalid request · `-32601` method not found · `-32602` invalid params (now
also resource-not-found) · `-32603` internal · spec-reserved slice **`-32020`…`-32099`**
(HeaderMismatch -32020, MissingRequiredClientCapability -32021, UnsupportedProtocolVersion
-32022). Tool business failure → **`result.isError: true`**, not an error object.

## Transports
stdio (local, subprocess, trusted machine) · Streamable HTTP (remote; POST + optional SSE; standard
headers `Mcp-Method`/`Mcp-Name`; custom via `x-mcp-header`) · old HTTP+SSE deprecated · **no stream
resumability** — re-issue with new request ID.

## Numbers
Version strings are dates (exam aligns **`2026-07-28`**) · D1 16% D2 14% D3 26% D4 24% D5 20%
· exam 90 min per LF page (AAIF says 120 — LF authoritative) · valid 2 yr · window 12 mo · 1 retake · Q-count unpublished.

## Security one-liners
- **PKCE mandatory**, OAuth 2.1, server = resource server.
- **No token passthrough** — validate `aud` (RFC 8707); metadata RFC 9728 well-known; validate `iss` (RFC 9207).
- **Client ID Metadata Documents** preferred; OAuth DCR (RFC 7591) deprecated.
- Tokens in `Authorization` header only, never URLs; keychain storage; short-lived.
- Tool list changed → **re-consent** (rug pull defense).
- Tool *results* = untrusted data (prompt injection / line-jumping).
- Confused deputy = misused legitimate authority → per-audience consent.
- Annotations (`readOnlyHint`…) are advisory; clients enforce.
- Audit: log calls + consents; least privilege; rate limits.

## Portability pitch
Server once → any MCP host; M×N → M+N; protocol is vendor-neutral (LF/AAIF).

## Portability pitch
Server once → any MCP host; M×N → M+N; protocol is vendor-neutral (LF/AAIF).
