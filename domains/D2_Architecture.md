# D2 — Architecture & Components (14%)

## Topology (✅ spec)

```
Host (Claude Desktop, IDE, agent platform)
 ├── MCP Client A  ←──stdio──→  MCP Server A (filesystem)
 ├── MCP Client B  ←──Streamable HTTP──→  MCP Server B (GitHub)
 └── MCP Client C  ←──stdio──→  MCP Server C (Postgres)
```

- The **host** aggregates multiple servers; each **client maintains exactly ONE connection to ONE
  server** (1:1). The host is the integrator, not the servers. ✅
- Clients never talk to each other; servers never talk to each other. Cross-server composition happens
  in the **host**. ✅

## JSON-RPC 2.0 message types (✅)

| Type | Has `id`? | Requires response? | Examples |
|---|---|---|---|
| Request | ✅ | ✅ | `tools/call`, `server/discover`, `resources/read` |
| Response | ✅ | — | `result` **or** `error` (never both); all results carry `resultType` (`"complete"` / `"input_required"`) |
| Notification | ❌ | ❌ | `notifications/cancelled`, progress, subscription events |

- A response contains **either** `result` **or** `error`. App-level failures often come back as
  `result.isError: true` on tool calls (see D3) — a favorite exam trap.

## Transport layer (✅)

| Transport | Mechanism | Typical use |
|---|---|---|
| **stdio** | newline-delimited JSON-RPC over stdin/stdout | local servers, launched as subprocesses |
| **Streamable HTTP** | HTTP POST for client→server + optional SSE stream for server→client | remote servers |
| HTTP+SSE (old) | two endpoints, GET SSE channel | **deprecated** — replaced by Streamable HTTP (🟡 still appears in older material) |

- stdio = highest-trust, same-machine, no network auth needed. Streamable HTTP = needs authorization
  (see D4). No session resumption: a broken stream loses the in-flight request; the client re-issues
  it with a **new request ID** (SSE resumability/`Last-Event-ID` was removed in 2026-07-28).

## Stateless core & capability declaration (✅ — the 2026-07-28 rewrite)

> **The old `initialize` handshake and `notifications/initialized` are GONE.** MCP is now stateless —
> this is the single biggest 2026-07-28 change and a guaranteed source of exam distractors (older
> tutorials and most competitor prep still teach the handshake).

- **No handshake**: every request carries its own context in `_meta`:
  `io.modelcontextprotocol/protocolVersion`, `io.modelcontextprotocol/clientCapabilities`; clients
  SHOULD send `clientInfo`, servers SHOULD return `serverInfo` in each result's `_meta`.
- **No sessions / no `Mcp-Session-Id`**: list endpoints no longer vary per-connection. Servers that
  need cross-call state mint explicit handles passed as ordinary tool arguments.
- **`server/discover`** (new, servers MUST implement): advertises supported protocol versions,
  capabilities, and identity. Clients MAY call it first for up-front version selection, or use it as
  a backward-compat probe on stdio.
- **Version mismatch** → `UnsupportedProtocolVersionError` (spec-reserved error code `-32022`).
- Capability objects remain per-primitive (e.g. `tools: {listChanged: true}`) plus a new
  `extensions` field on both sides for optional extensions (tasks ride there — see D3).
- **Never call a method the other side didn't declare.**

## Primitives: who exposes what (✅ — exam favorite)

| Direction | Primitives (server → client surface) | Discovery methods |
|---|---|---|
| Server exposes | **tools, resources, prompts** | `tools/list`, `resources/list`, `prompts/list` |
| Client exposes | **sampling, roots, elicitation handling** — all three features **deprecated** in 2026-07-28 | historically requested via server→client requests (`sampling/createMessage`, `roots/list`, `elicitation/create`); now wrapped in **MRTR** results (see D3) |

- Symmetry is the point: servers give capabilities; clients can also expose capabilities *to* servers.
- `serverInfo`/`clientInfo` (name+version) ride per-request/per-result in `_meta` — there is no
  initialize exchange to carry them anymore.

## Schemas

- Message shapes are defined in the spec's **TypeScript schema** + JSON Schema equivalents; inputs to
  tools are **JSON Schema** declared by the server (`inputSchema`) — clients use it to validate/render.
- Manifests: servers describe name, version, capabilities — "read a manifest and say what it supports"
  is a realistic question type.

## Exam angles (D2)

1. "How many servers can one MCP client connect to?" → **one** (1:1; the host fans out).
2. "A client wants to learn a server's supported versions/capabilities up front" → **`server/discover`**.
   Distractors offering `initialize`/`notifications/initialized` describe the pre-2026-07-28 protocol —
   removed.
3. "Which transport for a local subprocess server?" → stdio. "For a cloud SaaS server?" → Streamable HTTP.
4. "Server uses a capability the client never declared" → error; capabilities gate everything.
5. "Where do protocolVersion and client capabilities travel?" → in `_meta` **on every request** —
   not negotiated once in a handshake.
