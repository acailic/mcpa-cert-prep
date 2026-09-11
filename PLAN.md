# MCPA Cert-Prep — Full Operating Plan

**Status:** build phase (session 1, 2026-09-11) · **Owner:** acailic · **Factory:** DeepTutor agent workflow
**One-liner:** Be FIRST to market with a complete, original study pack for the Linux Foundation MCPA
(Model Context Protocol Associate) — the first official MCP certification — free pack as lead magnet,
paid bundle at GA, study-app as the later sellable asset.

---

## 1. Why this, why now (evidence)

- **Market:** LF MCPA is the first official MCP cert (beta Aug 4–17 2026; GA purchase still "notify me"
  as of Sep 11 2026 — cert-watch cron checks Mon+Thu 9:00, id `automation-9b1fa53d`). Zero dedicated
  prep products at GA window = first-strike (precedent: Sundog shipped CCDV-F practice exams in 7 days;
  Bonso/TutorialsDojo ~5 weeks → evergreen revenue).
- **Liquidity (live listing scan 2026-09-11, `../notes/market-scan-2026-09.md` §6–7):** AI study/education
  tools are the highest-multiple category on all four marketplaces: Acquire gamified quizzes $800k @ 4.2x,
  med-student learning agent $500k @ 4.3x, Flippa Summarizer.org $720k @ 5.3x, Little Exits study tool $10k
  @ 58 users, Microns math helper $1k. Education AI = 4.2–5.3x profit vs 1.7–2.5x generic wrappers.
- **Fit:** own pain (preparing for MCPA ourselves), factory exists (DeepTutor deep_question validated-quiz
  pipeline + MCP expertise), channel reachable (MCP dev community).

## 2. The exam (official, LF page fetched 2026-09-11)

| Domain | Weight |
|---|---|
| D1 MCP Fundamentals | 16% |
| D2 Architecture & Components | 14% |
| D3 Interactions & Execution | 26% |
| D4 Security & Governance | 24% |
| D5 Use Cases & Ecosystem | 20% |

- Online proctored MCQ · LF page: **90 min** (AAIF blog says 120 — 🟡 verify at GA) · validity **2 years**
  · eligibility window 12 months · **1 retake included** · money-back guarantee · beginner level.
- Aligned to MCP spec release **2026-07-28** (verify spec diff before exam-day content freeze).
- Recommended prerequisites: JSON-RPC basics, LLM API hands-on, agentic concepts, OAuth 2.1 basics,
  reading MCP server manifests.
- Price: beta was $99 (AAIF conference path −20%); GA price 🟡 unknown.

## 3. Product ladder

| Rung | Product | Price | Where | Purpose |
|---|---|---|---|---|
| 0 | **Free GitHub pack** (this repo): study plan, 5 domain guides, cheatsheet, glossary, common mistakes, 20 original Qs, flashcards + Anki deck, resources | $0 | github.com/acailic/mcpa-cert-prep | Lead magnet + validation |
| 1 | **Paid bundle**: 2×60-Q mock exams (timed, scaled scoring) + hard-questions-by-domain + API quick reference | **$19 launch → $29** | Payhip or Lemon Squeezy | First dollars |
| 2 | **MCPA email list** — "MCP cert track" newsletter (GA alerts, study tips, new-cert first-strikes) | free | linked from pack | The moat; later sells |
| 3 | **Web quiz app** (timed mocks, domain analytics, spaced repetition) | $9–15/mo or $49 one-time | Vercel | The sellable asset (comps above); build ONLY after rung 1 validates |

Skip: Udemy (37% margin + "no fully-AI-generated courses" policy), exam dumps (against LF policy, kills trust).

## 4. Validation gates (numbers, no vibes)

| Gate | Threshold | If missed |
|---|---|---|
| G1: free pack traction | **30 ⭐ + 20 email signups within 14 days of publish** | fix packaging/angle once; still <half → next cert |
| G2: paid bundle | **10 sales within 60 days of GA** (GA announced via cert-watch) | next cert; pack stays evergreen |
| G3: quiz app | only if G2 passed AND ≥30 bundle buyers ask for practice app | don't build otherwise |

Kill criteria pre-agreed (2026-09-03, prep-factory plan). Cert-watch automation already runs.

## 5. ONE channel: MCP developer community

Primary (all value-first, no spam): r/mcp · MCP Discord (modelcontextprotocol) · HN thread(s) on MCPA/MCP ·
AAIF announcements · X MCP community. Sequence: (1) participate answering MCP questions with depth for 1–2
weeks BEFORE publish, (2) publish pack with "free, original, unofficial" framing, (3) at GA: launch posts +
"what the exam actually covers" breakdown.
Secondary (only after G1 passes): shorts using before→after trend formats (ramd.am Sept 2026: fail-quiz →
study-with-[pack] → pass). Not now.

## 6. Legal & trust rules

- **Original questions only.** No real exam content, no dumps, no beta-recall questions.
- Prominent disclaimer: "Unofficial. Not affiliated with or endorsed by The Linux Foundation, AAIF, or
  Anthropic. MCPA is a trademark of its owner, used only to identify the exam."
- No LF/AAIF logos in pack art. Link the official cert page. License: content CC BY-NC-SA 4.0, code MIT.
- Grounding: MCP spec (modelcontextprotocol.io) + LF exam page; every factual claim tagged ✅ (official)
  or 🟡 (third-party). No fabricated "exam experience" reports.

## 7. Competitive watch (re-check weekly, cert-watch cron)

- CertiFluent MCPA study page (exists, thin) · dani_avila7 public beta study repo (beta taker) ·
  Udemy/Dumpscare sites (ignore/avoid) · **whomever ships a full practice-exam product first.**
- Our edge: depth (60% of exam = Interactions+Security — hands-on protocol behavior), original question
  bank, Anki deck, speed at GA.

## 8. Timeline

| When | Action |
|---|---|
| **Sep 11 (today, 5h session)** | this repo: full free pack built, committed, pushed private |
| Sep 12–13 | review pass, README polish, email-capture link (Payhip waitlist form), publish public, first value posts in community |
| Sep 12 → GA | cert-watch cron fires Mon/Thu; on GA alert: 48h runbook — publish paid bundle, launch posts, update all guides with GA price/logistics |
| GA + 60d | G2 verdict; if passed → rung 3 decision |

## 9. Costs

$0 fixed. Payhip/Lemon Squeezy take % only on sales. GitHub free. Domain optional later (only for quiz app).
