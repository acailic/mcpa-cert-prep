# D1 — MCP Fundamentals (16%)

## What MCP is

- **Model Context Protocol**: an open protocol that standardizes how applications provide context,
  tools, and prompts to LLMs. ✅
- Solves the **M×N integration problem**: without MCP, every AI app × every data source needs a custom
  connector (M×N work). With MCP, both sides implement once (M+N work). ✅
- Analogy the exam likes: **"USB-C port for AI applications"** — one standardized port for plugging
  tools/data into any model-backed app. ✅
- Built on **JSON-RPC 2.0** — every MCP message is a JSON-RPC request, response, or notification. ✅
- Origin & governance: initiated by Anthropic (open-sourced Nov 2024); adopted under neutral stewardship
  via the Agentic AI Foundation / Linux Foundation (🟡 details moved during 2025–2026 — don't over-study
  governance history).

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
| **Sampling** | server asks the *client* to run an LLM completion (server never calls models directly) |
| **Roots** | client tells the server which filesystem/URI boundaries it may work in |
| **Elicitation** | server asks the *user* (via client) for structured input mid-flow 🟡 newer spec |

## Control hierarchy (✅ — classic exam question)

| Primitive | Who controls it? |
|---|---|
| Tools | **Model** (LLM decides) |
| Resources | **Application** (host decides what context to attach) |
| Prompts | **User** (user explicitly selects) |
| Sampling | **Server requests, client executes** (user approves) |

## Interoperability & versioning

- **Protocol version** is a date string (e.g. `2025-06-18`); negotiated in the `initialize` handshake —
  client proposes, server responds with the version it supports. ✅
- **Capability negotiation**: each side declares what it supports (tools, resources, prompts, sampling…)
  in `initialize`; you only use what was negotiated. ✅
- MCPA scope is aligned to spec release **2026-07-28** — if you learned MCP pre-2026, re-diff the spec
  (🟡 changelog is the suspect list: elicitation, tasks, registry updates).

## Interoperability angles the exam tests

- "Write once, run in any MCP host" — servers are host-agnostic; that's the portability pitch. ✅
- MCP is **transport-agnostic**: same protocol over stdio, Streamable HTTP, (custom) sockets. ✅
- JSON-RPC 2.0 means: id-less **notifications** (fire-and-forget), id-bearing **requests** (require a
  response), and **batch** semantics (🟡 batch support changed across spec revisions).

## Exam angles (D1)

1. "Why does MCP exist?" → M×N → M+N standardization; pick answers about *standardization*, not
   performance or model quality.
2. "Who controls prompts/resources/tools?" → user/app/model — memorize the table.
3. "What runs the LLM when a server needs sampling?" → the **client's** model, with user approval.
4. Distractors claiming MCP replaces REST/GraphQL for web apps — wrong scope: MCP connects AI apps to
   context/tools.
