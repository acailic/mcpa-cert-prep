# MCPA Cheatsheet — cram page

## Lifecycle order (memorize)
`initialize` → server replies (version+capabilities) → `notifications/initialized` → normal traffic
(`tools/list` → `tools/call` …) → shutdown. Never call a method before initialized; never call outside
negotiated capabilities.

## Who controls what
| Primitive | Control |
|---|---|
| tools | model |
| resources | application (host) |
| prompts | user |
| sampling | server requests → **client's LLM** runs it (user approves) |
| roots | client defines boundaries (advisory) |
| elicitation 🟡 | server asks **user** for structured input |

## Message types (JSON-RPC 2.0)
- Request: has `id`, must get a response.
- Response: `result` **XOR** `error`.
- Notification: no `id`, no reply (`notifications/initialized`, `/cancelled`, `/progress`, `/message`,
  `tools/list_changed`, `resources/updated`).

## Methods table
| Method | Direction | Notes |
|---|---|---|
| `initialize` | c→s | protocolVersion (date str), capabilities, clientInfo |
| `ping` | both | liveness, empty result |
| `tools/list` `tools/call` | c→s | inputSchema JSON Schema; annotations = hints |
| `resources/list` `resources/read` | c→s | URI + templates; text/blob + mimeType |
| `resources/subscribe` `resources/unsubscribe` | c→s | → `notifications/resources/updated` |
| `prompts/list` `prompts/get` | c→s | returns messages array |
| `completion/complete` | c→s | argument autocomplete |
| `logging/setLevel` | c→s | then `notifications/message` events |
| `sampling/createMessage` | **s→c** | client's model + user approval |
| `roots/list` | **s→c** | client exposes URI boundaries |
| `elicitation/create` 🟡 | **s→c** | structured user input |

## Error codes
`-32700` parse · `-32600` invalid request · `-32601` method not found · `-32602` invalid params ·
`-32603` internal. Tool business failure → **`result.isError: true`**, not an error object.

## Transports
stdio (local, subprocess, trusted machine) · Streamable HTTP (remote; POST + optional SSE; session ID)
· old HTTP+SSE deprecated.

## Numbers
Version strings are dates (`2025-06-18`; exam aligns `2026-07-28`) · D1 16% D2 14% D3 26% D4 24% D5 20%
· exam 90 min (🟡120) · valid 2 yr · window 12 mo · 1 retake · ~60 Q (🟡).

## Security one-liners
- **PKCE mandatory**, OAuth 2.1, server = resource server.
- **No token passthrough** — validate `aud` (RFC 8707); metadata RFC 9728 well-known.
- Tokens in `Authorization` header only, never URLs; keychain storage; short-lived.
- Session IDs: secure-random, origin-validated, expire.
- Tool list changed → **re-consent** (rug pull defense).
- Tool *results* = untrusted data (prompt injection / line-jumping).
- Confused deputy = misused legitimate authority → per-audience consent.
- Annotations (`readOnlyHint`…) are advisory; clients enforce.
- Audit: log calls + consents; least privilege; rate limits.

## Portability pitch
Server once → any MCP host; M×N → M+N; protocol is vendor-neutral (LF/AAIF).
