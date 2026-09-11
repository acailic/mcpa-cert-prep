# MCPA Exam Guide — logistics + domain map

> ✅ = from the official LF certification page / MCP spec · 🟡 = third-party, verify before exam day.

## Logistics (✅ official LF page, fetched 2026-09-11)

| Item | Value |
|---|---|
| Exam | Model Context Protocol Associate (**MCPA**) — first official MCP certification |
| Issuer | Linux Foundation / Agentic AI Foundation (AAIF) |
| Format | Online, **proctored**, multiple-choice |
| Duration | **90 min** per LF page (🟡 AAIF launch post says 120 min — check at purchase) |
| Validity | **2 years** |
| Eligibility window | 12 months from purchase |
| Retakes | **1 retake included** |
| Guarantee | 100% money-back |
| Level | Beginner (no prerequisites listed) |
| Availability | GA purchase "notify me" as of 2026-09-11 (beta ran Aug 4–17 2026); conference path −20% via AAIF |
| Price | 🟡 beta was $99 (GA unknown) |

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

- 60% of the exam = D3 + D4. These are *behavioral* (message flows, consent, OAuth), not vocabulary —
  drill the lifecycle and the security traps until boring.
- Expect scenario questions: "a client receives X, what happens next?" — learn the message ordering.
- MCPA is vendor-neutral: questions stay protocol-level (no Anthropic/OpenAI product specifics).
- Content freeze rule: re-diff the MCP spec `2025-06-18` → `2026-07-28` before exam day (🟡 flagged
  items in these guides are the suspects).
