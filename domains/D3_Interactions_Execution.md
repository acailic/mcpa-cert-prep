# D3 — Interactions & Execution (26% — the biggest domain)

Master the message flows. Most exam scenarios here are "what happens next / what does the client receive".

## The tool invocation lifecycle (✅)

```
client → tools/list          → server returns [{name, description, inputSchema, annotations?}] (deterministic order)
client → tools/call {name, arguments}
       ← progress notifications (optional, notifications/progress {progressToken})
       ← result {resultType: "complete", content: [...], isError?: false}
client → notifications/cancelled {requestId, reason?}   (if user aborts)
```

- **Every result now carries a required `resultType`**: `"complete"` (ordinary) or `"input_required"`
  (MRTR interim — see below). Clients MUST treat results that omit the field (older servers) as
  `"complete"`.
- **Discovery**: `tools/list` returns the full list in a **deterministic order** (2026-07-28 — enables
  client caching / prompt-cache hits); list results are now `CacheableResult`s carrying `ttlMs` +
  `cacheScope`. `listChanged` notifications tell clients to re-list. Tool *annotations* are hints
  (`readOnlyHint`, `destructiveHint`, `idempotentHint`, `openWorldHint`) — **hints, not guarantees**
  (exam trap: annotations ≠ enforcement).
- Arguments are validated against the tool's declared **JSON Schema `inputSchema`** (2026-07-28 loosened
  to any JSON Schema 2020-12 keywords).

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
| `-32602` | Invalid params (also the **new** resource-not-found code — moved from `-32002` in 2026-07-28) |
| `-32603` | Internal error |

MCP reserves its own slice of the server-error range (2026-07-28): **`-32020`…`-32099`** for the spec
(e.g. `HeaderMismatch` `-32020`, `MissingRequiredClientCapability` `-32021`,
`UnsupportedProtocolVersion` `-32022`); `-32000`…`-32019` stays implementation-defined.

## Resources (✅)

- `resources/list` (+ pagination via opaque `cursor`), `resources/read {uri}` → contents: `text` or
  `blob` (base64) + `mimeType`.
- **URI templates** (`file:///{path}`, `github://repos/{owner}/{repo}/...`) — servers advertise
  `resources/templates/list`.
- **Subscriptions (2026-07-28 shape)**: the old `resources/subscribe`/`resources/unsubscribe` and the
  HTTP GET endpoint are **replaced by `subscriptions/listen`** — one long-lived POST-response stream.
  Clients opt in to notification types (`toolsListChanged`, `promptsListChanged`, `resourcesListChanged`,
  `resourceSubscriptions`); the server tags events with `io.modelcontextprotocol/subscriptionId`.
  Legacy distractor: `resources/subscribe` — pre-rewrite.
- Resources are **application-controlled** (host decides what to attach) and conceptually read-only.

## Prompts (✅)

- `prompts/list`, `prompts/get {name, arguments}` → returns a **messages array** (role + content),
  can embed text, images, and **embedded resources**.
- User-invoked (slash-command style), argument **autocompletion** via `completion/complete`.

## Sampling (⚠️ deprecated 2026-07-28 — but still exam-relevant)

- **Feature status**: Sampling (with Roots and Logging) is **Deprecated** in 2026-07-28 (SEP-2577) —
  functional during the ≥12-month window, but **new implementations should not adopt it**; migration =
  call LLM provider APIs directly.
- Old shape (and the answer when the exam asks what the feature *is*): server needs an LLM completion →
  the **client** runs it with the user's configured model after user approval; server only *suggests*
  modelPreferences, client enforces. Since 2026-07-28 the request no longer travels as a standalone
  server→client request — it is wrapped in MRTR (below).

## Roots (⚠️ deprecated 2026-07-28)

- Client-declared **URI boundaries** (e.g. `file:///home/user/project`) — advisory scoping, never a
  security enforcement (exam trap: roots ≠ sandbox). Deprecated alongside Sampling/Logging; migration =
  pass directories/files via tool parameters, resource URIs, or server config. `notifications/roots/list_changed`
  is **removed**.

## MRTR — Multi Round-Trip Requests (✅ NEW in 2026-07-28 — high-yield)

- **Servers no longer send standalone server→client requests** (`sampling/createMessage`, `roots/list`,
  `elicitation/create` are replaced). Instead:
  1. Server returns an **`InputRequiredResult`** — `resultType: "input_required"` — whose
     **`inputRequests`** array carries the requests for the extra information.
  2. Client gathers the info (may mean asking the user — that's where elicitation now lives).
  3. Client **retries the original request** with **`inputResponses`** attached.
- `notifications/elicitation/complete` (added 2025-11-25, removed again) is gone — the client learns
  out-of-band outcomes by retrying; servers correlate via their own `requestState`.
- Contrast that survives the rewrite: sampling asks the *model*; elicitation asks the *user*.

## Tasks (✅ — now an extension, not core)

- Long-running operations moved **out of core** into the official extension
  **`io.modelcontextprotocol/tasks`** (SEP-2663): servers return task handles (unsolicited OK);
  clients poll **`tasks/get`**, send input via **`tasks/update`**; `tasks/list` was removed;
  `notifications/tasks` for updates. Legacy distractor: `tasks/result` blocking wait — replaced by polling.

## Cross-cutting execution mechanics (✅)

| Mechanism | How |
|---|---|
| **Progress** | client passes `progressToken` in `_meta`; server emits `notifications/progress {progressToken, progress, total?}` |
| **Cancellation** | `notifications/cancelled {requestId}` — in-flight work *should* stop; result may still arrive and is discarded |
| **Logging** ⚠️ deprecated | old: client set minimum level via `logging/setLevel` → server emitted `notifications/message`. Now: **no `logging/setLevel`**; log level is set **per-request** via `io.modelcontextprotocol/logLevel` in `_meta`; servers MUST NOT emit `notifications/message` for requests that didn't ask. Feature deprecated — migrate to stderr/stdio or OpenTelemetry. |
| **Pagination** | opaque `cursor` in/out (`nextCursor`) — never parse the cursor, just pass it back |
| **Completions** | `completion/complete` for prompt/resource-template argument suggestions |
| ~~Ping~~ | **removed in 2026-07-28** — `ping` no longer exists; distractor answers citing it describe the old protocol |

## Interaction patterns the exam scenarios love

1. **Re-listing after change notifications**: `listChanged` → client must re-call `*/list`.
2. **Cancellation timing**: cancel is a *notification* — no response, no error; late results are dropped.
3. **Long tool calls**: progress token optional; absence of progress ≠ failure. Very long? → tasks
   extension (poll `tasks/get`), not a blocking `tasks/result` (removed).
4. **isError path**: file-missing tool returns `isError: true`, NOT a JSON-RPC error.
5. **Capability gate**: server never advertised `tools` → `tools/list` returns method-not-found error.
6. **Pagination trap**: passing a *made-up* cursor → error; cursors are opaque.
7. **MRTR flow**: result arrives with `resultType: "input_required"` → answer lives in
   `inputResponses` on the *retry of the same request* — not in a new server→client exchange.

## Exam angles (D3)

- Flow questions now center on **MRTR** (`input_required` → retry with `inputResponses`) and
  `subscriptions/listen` — the initialize→initialized ordering questions are pre-rewrite distractors.
- "Which method does X?" table memorization (read/listen/sampling/elicitation-as-MRTR/tasks).
- Distinguish: notification (no id, no reply) vs request (id, must reply).
- Error taxonomy: parse/invalid request/method not found/invalid params/internal + `isError` + the
  spec-reserved `-32020…-32099` range.
