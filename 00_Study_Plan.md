# 2-Week Study Plan (weighted to the exam)

Rule: **study time follows domain weights.** D3 (26%) + D4 (24%) get half your hours.

## Week 1 — learn

| Day | Hours | Task |
|---|---|---|
| 1 | 1.5 | [01_Exam_Guide](01_Exam_Guide.md) + [domains/D1](domains/D1_Fundamentals.md) + install an MCP host + one reference server, watch the traffic (stateless now — no handshake to watch) |
| 2 | 2 | [domains/D2](domains/D2_Architecture.md) — sketch host/client/server diagram from memory; list JSON-RPC message types; memorize the 2026-07-28 removals/deprecations |
| 3 | 2.5 | [domains/D3](domains/D3_Interactions_Execution.md) part 1: tools (list/call/errors/progress/cancel) + MRTR pattern |
| 4 | 2.5 | D3 part 2: resources + subscriptions/listen, prompts, sampling/roots/elicitation (deprecated + MRTR), pagination, logging |
| 5 | 2.5 | [domains/D4](domains/D4_Security_Governance.md) part 1: trust model, consent, confused deputy, token passthrough |
| 6 | 2.5 | D4 part 2: OAuth 2.1 flows, session security, tool poisoning, auditability |
| 7 | 1 | [domains/D5](domains/D5_UseCases_Ecosystem.md) + [03_Cheatsheet](03_Cheatsheet.md) first pass |

## Week 2 — drill

| Day | Hours | Task |
|---|---|---|
| 8 | 1 | Free quiz ([quizzes/Quiz_Free_20q.md](quizzes/Quiz_Free_20q.md)) cold → score by domain |
| 9–11 | 3×1.5 | Reread your 2 weakest domain guides; redo their flashcards ([anki/](anki/MCPA_Anki.tsv)) |
| 12 | 1.5 | Free quiz again — target **≥15/20**; write down every miss's *reason*, not just the answer |
| 13 | 1 | [08_Common_Mistakes.md](08_Common_Mistakes.md) + [03_Cheatsheet.md](03_Cheatsheet.md) |
| 14 | — | rest / skim cheatsheet only |

## Daily 15-minute habit

Anki deck ([anki/MCPA_Anki.tsv](anki/MCPA_Anki.tsv)) — 10 cards/day beats weekend cramming.

## Readiness bar (free-pack version)

- Free quiz ≥ 15/20 with D3+D4 ≥ 70%
- You can draw the tools/list → tools/call flow (with MRTR `input_required` branch) from memory
- You can name 5 security risks and their mitigations without looking
