# D5 — Use Cases & Ecosystem (20%)

## Adoption roles (✅ framing from LF exam description)

| Role | What they do | Cares about |
|---|---|---|
| **Server developers** | expose tools/data/prompts for a system (GitHub, Postgres, Figma…) | correct schemas, good descriptions, security |
| **Client / host builders** | IDEs, desktop apps, agent platforms that speak MCP | transport, auth UX, consent, aggregation |
| **Platform / enterprise teams** | govern MCP usage org-wide | allowlists, audit, data boundaries |
| **AI engineers / agents devs** | consume servers inside products | portability, cost, reliability |

## Operational use cases (✅ typical set)

- **Developer workflows**: IDE + repo/filesystem servers, issue trackers, CI logs ("fix the failing test").
- **Data access**: SQL/DB servers with read-scoped credentials; analytics via resource subscriptions.
- **Enterprise knowledge**: document stores behind governed servers (permissions preserved server-side).
- **Web/browser automation** and API wrapping (legacy SaaS without LLM support → MCP server in front).
- **Agent tooling**: search, code execution, external actions with human approval gates.
- Anti-patterns to recognize: MCP as a *general-purpose public REST replacement*; exposing
  fully-privileged DB creds to satisfy one read query; auto-approving every tool to "reduce friction".

## Ecosystem & portability (✅)

- **Write once, any host**: a conforming server works in any MCP client (the core value proposition).
- **Official reference servers** (filesystem, git, fetch, memory…) — community + vendor servers listed in
  directories/registries. The **official Registry (registry.modelcontextprotocol.io) is still in PREVIEW**,
  run by the modelcontextprotocol project (not AAIF itself) — enterprises build allowlists rather than
  trusting it blindly.
- **Versioning in practice**: date-based protocol versions + per-request `_meta` declaration (2026-07-28:
  stateless — no handshake) = graceful degradation; `server/discover` reveals what a server supports;
  extensions (e.g. tasks) let servers ship beyond-core features without breaking core clients.
- **Interoperability** questions compare MCP to alternatives: function/tool calling (vendor-specific,
  in-process), OpenAPI plugins (web-centric), MCP (protocol-level, host-agnostic, cross-vendor).

## Governance & adoption in organizations (✅ scenarios)

1. **Approved servers + allowlists**: platform team curates which servers may connect.
2. **Data boundaries**: roots + scoped credentials per server; least privilege.
3. **Audit**: central logging of tool calls & consents (feeds SIEM).
4. **Secrets**: servers hold their own credentials (scoped), clients hold user tokens (audience-bound).
5. **Rollout pattern**: pilot with read-only servers → consent UX review → expand to writes with
   approval gates.

## When NOT to use MCP (exam distractors)

- Simple single-app integrations where one vendor SDK suffices (MCP adds protocol overhead).
- Pure model-provider API needs (chat/completions) — that's not MCP's job.
- Hard-realtime or high-throughput data-plane work (MCP is a control/context plane).

## Community & standards landscape (🟡 light-touch, verify)

- Spec repo: github.com/modelcontextprotocol (specification, SDKs: TypeScript, Python, Go, Kotlin, C#, …).
- Governance: Agentic AI Foundation (AAIF) / Linux Foundation umbrella.
- MCPA itself is the ecosystem signal: vendor-neutral skills credential for MCP engineers.

## Exam angles (D5)

- "Company wants every team to use MCP safely" → allowlist + audit + scoped credentials + consent UX
  (pick the *governance* answer, not "ban servers").
- "Why choose MCP over per-vendor function calling?" → vendor-neutral portability, host-agnostic.
- "Who benefits when a new host ships?" → *all existing servers* work without modification (portability).
- Match role→responsibility (server dev vs client dev vs platform team).
