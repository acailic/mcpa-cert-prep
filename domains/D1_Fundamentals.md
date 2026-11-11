# D1 — MCP Fundamentals (16%)

## What MCP is

- **Model Context Protocol**: an open protocol that standardizes how applications provide context,
  tools, and prompts to LLMs. ✅
- Solves the **M×N integration problem**: without MCP, every AI app × every data source needs a custom
  connector (M×N work). With MCP, both sides implement once (M+N work). ✅
- Analogy the exam likes: **"USB-C port for AI applications"** — one standardized port for plugging
  tools/data into any model-backed app. ✅
- Built on **JSON-RPC 2.0** — every MCP message is a JSON-RPC request, response, or notification. ✅
- Origin & governance: initiated by Anthropic (open-sourced Nov 2024); donated to the **Agentic AI
  Foundation (AAIF)** under the Linux Foundation (Dec 2025) — "a Series of LF Projects, LLC"; neutral
  stewardship since. 2026-07-28 also added a **feature lifecycle policy** (Active / Deprecated /
  Removed, ≥12-month deprecation window) — expect governance questions to cite it.

## What MCP is NOT (classic distractors)

- ❌ Not an LLM API standard (it doesn't standardize model calls — sampling is delegated to the client's
  configured model).
- ❌ Not an agent framework or orchestration library (no planning/memory primitives).
- ❌ Not a transport-specific technology (HTTP is one transport; stdio is another).
- ❌ Not a model or product — a *protocol specification* anyone can implement.

## Core concepts (✅ spec)

| Concept | One-liner |
|---|---|
| **Host** | the AI application the user interacts with (IDE, desktop app, agent platform) |
| **Client** | connector inside the host; **one client ↔ one server** connection |
| **Server** | program exposing capabilities (tools/resources/prompts) over MCP |
| **Tools** | model-controlled actions — the LLM decides when to call them |
| **Resources** | application-controlled data the host/user attaches — read-only context |
| **Prompts** | user-controlled templates the user explicitly picks |
| **Sampling** | server asks the *client* to run an LLM completion (server never calls models directly) — **deprecated 2026-07-28** |
| **Roots** | client tells the server which filesystem/URI boundaries it may work in — **deprecated 2026-07-28** |
| **Elicitation** | server asks the *user* (via client) for structured input mid-flow — since 2026-07-28 delivered via **MRTR**, not a standalone server→client request |

## Control hierarchy (✅ — classic exam question)

| Primitive | Who controls it? |
|---|---|
| Tools | **Model** (LLM decides) |
| Resources | **Application** (host decides what context to attach) |
| Prompts | **User** (user explicitly selects) |
| Sampling | **Server requests, client executes** (user approves) |

## Interoperability & versioning

- **Protocol version** is a date string (e.g. `2026-07-28`). Since the 2026-07-28 rewrite the protocol is
  **stateless**: there is **no `initialize` handshake** — every request carries its version and the
  client's capabilities in `_meta` (`io.modelcontextprotocol/protocolVersion`,
  `io.modelcontextprotocol/clientCapabilities`). Mismatch → `UnsupportedProtocolVersionError`.
- **Capability declaration**: each side declares what it supports (tools, resources, prompts,
  extensions…) — per-request in `_meta`; you only use what was declared. **`server/discover`** lets a
  client learn versions/capabilities/identity up front.
- MCPA scope is aligned to spec release **2026-07-28** — if you learned MCP pre-2026, re-diff: the
  changelog (initialize/sessions/ping removed; MRTR added; sampling/roots/logging, HTTP+SSE, and OAuth
  DCR deprecated; tasks → extension).

## Interoperability angles the exam tests

- "Write once, run in any MCP host" — servers are host-agnostic; that's the portability pitch. ✅
- MCP is **transport-agnostic**: same protocol over stdio, Streamable HTTP, (custom) sockets. ✅
- JSON-RPC 2.0 means: id-less **notifications** (fire-and-forget) and id-bearing **requests** (require a
  response). **Batching was removed in 2025-06-18** — an answer proposing batched requests is a
  pre-2025 distractor.

## Exam angles (D1)

1. "Why does MCP exist?" → M×N → M+N standardization; pick answers about *standardization*, not
   performance or model quality.
2. "Who controls prompts/resources/tools?" → user/app/model — memorize the table.
3. "What runs the LLM when a server needs sampling?" → the **client's** model, with user approval.
4. Distractors claiming MCP replaces REST/GraphQL for web apps — wrong scope: MCP connects AI apps to
   context/tools.
