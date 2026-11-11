# MCPA Exam Guide — logistics + domain map

> ✅ = from the official LF certification page / MCP spec · 🟡 = third-party, verify before exam day.

## Logistics (✅ official LF page, verified 2026-11-11)

| Item | Value |
|---|---|
| Exam | Model Context Protocol Associate (**MCPA**) — first official MCP certification |
| Issuer | Linux Foundation / Agentic AI Foundation (AAIF) |
| Format | Online, **proctored**, multiple-choice |
| Duration | **90 min per the LF cert page** (✅ verified 2026-11-11). AAIF's July blog and Sept press release say **120 min** — official sources conflict. The LF purchase page is authoritative; most third-party prep repeats 120 without checking. Confirm on your checkout page. |
| Validity | **2 years** |
| Eligibility window | 12 months from purchase |
| Retakes | **1 retake included** |
| Guarantee | 100% money-back |
| Level | Beginner (no prerequisites listed) |
| Availability | **GA live since 2026-09-13** — Enroll Today, $250. THRIVE-ONE bundle $495 (−40% multi-cert). Conference path −20% (AGNTCon+MCPCon NA, Oct 2026). Beta ran Aug 4–17 2026. |
| Price | **$250** (✅ GA; beta was $99) |

## Recommended prerequisites (✅ LF page)

- Basic **JSON-RPC** or similar message-based protocol knowledge
- Hands-on use of **LLM APIs** (OpenAI / Anthropic / local via Ollama)
- Familiarity with **agentic AI** concepts (ReAct, tool-use, chain-of-thought)
- Security basics: **API keys, OAuth 2.1 and token handling**, auth headers
- Ability to read **MCP server manifests and capability definitions**

## Domain map + study weight (✅ official)

| # | Domain | Weight | ~Q of 60 | Focus |
|---|---|---|---|---|
| D1 | MCP Fundamentals | 16% | ~10 | purpose/scope, core concepts, interoperability |
| D2 | Architecture & Components | 14% | ~8 | schemas, hosts/clients/servers, model interaction flow |
| D3 | Interactions & Execution | 26% | ~16 | interaction patterns, error handling, tool invocation lifecycle, primitives |
| D4 | Security & Governance | 24% | ~14 | trust boundaries, permissions & consent, risk & safety, auditability |
| D5 | Use Cases & Ecosystem | 20% | ~12 | roles & adoption, operational use cases, ecosystem & portability |

Exam scope statement (✅): "how the protocol works and how its components communicate" — architecture,
message flow, client-server interactions, capabilities, tools, resources, prompts, transports, security.
Spec alignment: release **2026-07-28**.

## Strategy notes

- **2026-07-28 is a fundamental rewrite** (stateless core, MRTR, deprecations — see the rewrite box in
  D2/D3). Older tutorials and most competitor prep still teach the pre-rewrite handshake; expect those
  mechanics to appear as *distractors*. Every guide in this pack is verified against the current spec
  (changelog re-checked 2026-11-11).
- 60% of the exam = D3 + D4. These are *behavioral* (message flows, consent, OAuth), not vocabulary —
  drill the flows (MRTR, subscriptions, cancellation) and the security traps until boring.
- Expect scenario questions: "a client receives X, what happens next?" — learn the message ordering.
- MCPA is vendor-neutral: questions stay protocol-level (no Anthropic/OpenAI product specifics).
- Content freeze rule: re-check for spec releases after `2026-07-28` before exam day (none as of
  2026-11-11).
