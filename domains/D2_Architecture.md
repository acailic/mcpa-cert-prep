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
| Request | ✅ | ✅ | `tools/call`, `initialize`, `resources/read` |
| Response | ✅ | — | `result` **or** `error` (never both) |
| Notification | ❌ | ❌ | `notifications/initialized`, `notifications/cancelled`, progress |

- A response contains **either** `result` **or** `error`. App-level failures often come back as
  `result.isError: true` on tool calls (see D3) — a favorite exam trap.

## Transport layer (✅)

| Transport | Mechanism | Typical use |
|---|---|---|
| **stdio** | newline-delimited JSON-RPC over stdin/stdout | local servers, launched as subprocesses |
| **Streamable HTTP** | HTTP POST for client→server + optional SSE stream for server→client | remote servers |
| HTTP+SSE (old) | two endpoints, GET SSE channel | **deprecated** — replaced by Streamable HTTP (🟡 still appears in older material) |

- stdio = highest-trust, same-machine, no network auth needed. Streamable HTTP = needs authorization
  (see D4), resumability via session ID.

## Lifecycle & capability negotiation (✅)

1. Client → `initialize` request: `protocolVersion` (date string), `capabilities`, `clientInfo`.
2. Server → response: **its** `protocolVersion`, **its** `capabilities`, `serverInfo`, optional
   `instructions`.
3. Client → `notifications/initialized` — only now may normal requests begin.
- Capability objects are per-primitive with flags like `listChanged` (server will notify when the list
  changes): `tools: {listChanged: true}`. **Never call a method outside negotiated capabilities.**
- Version negotiation rule: client proposes its latest; server replies with **a version it supports**;
  if client can't support the server's reply → it disconnects.

## Primitives: who exposes what (✅ — exam favorite)

| Direction | Primitives (server → client surface) | Discovery methods |
|---|---|---|
| Server exposes | **tools, resources, prompts** | `tools/list`, `resources/list`, `prompts/list` |
| Client exposes | **sampling, roots, elicitation handling** | requested by server via `sampling/createMessage`, `roots/list`, `elicitation/create` 🟡 |

- Symmetry is the point: servers give capabilities; clients can also expose capabilities *to* servers.
- `serverInfo`/`clientInfo` (name+version) and optional `instructions` ride in the initialize exchange.

## Schemas

- Message shapes are defined in the spec's **TypeScript schema** + JSON Schema equivalents; inputs to
  tools are **JSON Schema** declared by the server (`inputSchema`) — clients use it to validate/render.
- Manifests: servers describe name, version, capabilities — "read a manifest and say what it supports"
  is a realistic question type.

## Exam angles (D2)

1. "How many servers can one MCP client connect to?" → **one** (1:1; the host fans out).
2. "What happens right after the server responds to `initialize`?" → client sends
   `notifications/initialized`; *not* `tools/list` yet.
3. "Which transport for a local subprocess server?" → stdio. "For a cloud SaaS server?" → Streamable HTTP.
4. "Server uses a method the client never negotiated" → error; capabilities gate everything.
