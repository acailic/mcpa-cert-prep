# D3 — Interactions & Execution (26% — the biggest domain)

Master the message flows. Most exam scenarios here are "what happens next / what does the client receive".

## The tool invocation lifecycle (✅)

```
client → tools/list          → server returns [{name, description, inputSchema, annotations?}]
client → tools/call {name, arguments}
       ← progress notifications (optional, notifications/progress {progressToken})
       ← result {content: [...], isError?: false}
client → notifications/cancelled {requestId, reason?}   (if user aborts)
```

- **Discovery is static-per-session**: `tools/list` returns the full list; `tools/listChanged`
  notification tells clients to re-list. Tool *annotations* are hints (`readOnlyHint`, `destructiveHint`,
  `idempotentHint`, `openWorldHint`) — **hints, not guarantees** (exam trap: annotations ≠ enforcement).
- Arguments are validated against the tool's declared **JSON Schema `inputSchema`**.

## Tool results vs protocol errors (✅ — the #1 trap)

| Situation | What comes back |
|---|---|
| Tool ran; operation failed (file not found, API 500) | **success response** with `isError: true` + message in `content` |
| Unknown tool / invalid arguments / no capability | **JSON-RPC error response** (e.g. `-32602`) |
| Transport/parser broken | JSON-RPC `error` codes (`-32700`, `-32600`) |

So: *business* failure ≠ protocol error. The model reads `isError` results as feedback and may retry.

## Standard JSON-RPC error codes (✅)

| Code | Meaning |
|---|---|
| `-32700` | Parse error |
| `-32600` | Invalid request |
| `-32601` | Method not found |
| `-32602` | Invalid params |
| `-32603` | Internal error |

## Resources (✅)

- `resources/list` (+ pagination via opaque `cursor`), `resources/read {uri}` → contents: `text` or
  `blob` (base64) + `mimeType`.
- **URI templates** (`file:///{path}`, `github://repos/{owner}/{repo}/...`) — servers advertise
  `resources/templates/list`.
- **Subscriptions**: `resources/subscribe {uri}` → server sends `notifications/resources/updated`
  → client re-reads. Directories: `notifications/resources/list_changed`.
- Resources are **application-controlled** (host decides what to attach) and conceptually read-only.

## Prompts (✅)

- `prompts/list`, `prompts/get {name, arguments}` → returns a **messages array** (role + content),
  can embed text, images, and **embedded resources**.
- User-invoked (slash-command style), argument **autocompletion** via `completion/complete`.

## Sampling (✅ — server never talks to a model directly)

- Server → client: `sampling/createMessage` (system prompt, messages, modelPreferences, maxTokens).
- **Client** runs it with the user's configured LLM, **after user approval**, returns the completion.
- Security point: server only *suggests* model preferences; client enforces (ties into D4).

## Roots (✅)

- Client → `roots/list` (server asks) or client pushes `notifications/roots/list_changed`.
- Roots are **URI boundaries** (e.g. `file:///home/user/project`) — advisory scoping, not a security
  enforcement (exam trap: roots ≠ sandbox).

## Elicitation (🟡 newer spec — in 2026-07-28 scope)

- Server → client `elicitation/create`: request **structured input from the user mid-flow**
  (schema-constrained form). Client mediates; user may decline. Server pauses awaiting the response.
- Contrast with sampling: sampling asks the *model*; elicitation asks the *user*.

## Cross-cutting execution mechanics (✅)

| Mechanism | How |
|---|---|
| **Ping** | `ping` request → empty result; liveness check either direction |
| **Progress** | client passes `progressToken` in `_meta`; server emits `notifications/progress {progressToken, progress, total?}` |
| **Cancellation** | `notifications/cancelled {requestId}` — in-flight work *should* stop; result may still arrive and is discarded |
| **Logging** | client sets minimum level (`logging/setLevel`); server emits `notifications/message {level, data}` — debug/info/notice/warning/error |
| **Pagination** | opaque `cursor` in/out (`nextCursor`) — never parse the cursor, just pass it back |
| **Completions** | `completion/complete` for prompt/resource-template argument suggestions |

## Interaction patterns the exam scenarios love

1. **Re-listing after change notifications**: `listChanged` → client must re-call `*/list`.
2. **Cancellation timing**: cancel is a *notification* — no response, no error; late results are dropped.
3. **Long tool calls**: progress token optional; absence of progress ≠ failure.
4. **isError path**: file-missing tool returns `isError: true`, NOT a JSON-RPC error.
5. **Capability gate**: server never advertised `tools` → `tools/list` returns method-not-found error.
6. **Pagination trap**: passing a *made-up* cursor → error; cursors are opaque.

## Exam angles (D3)

- Order-of-events questions (initialize → notifications/initialized → tools/list → tools/call).
- "Which method does X?" table memorization (read/subscribe/sampling/elicitation).
- Distinguish: notification (no id, no reply) vs request (id, must reply).
- Error taxonomy: parse/invalid request/method not found/invalid params/internal + `isError`.
