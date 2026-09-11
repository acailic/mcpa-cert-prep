# Official resources (read these, in this order)

1. **LF MCPA certification page** — exam description, domain weights, logistics.
   https://training.linuxfoundation.org/certification/model-context-protocol-associate-mcpa/
2. **MCP specification** (exam aligns to release 2026-07-28):
   https://modelcontextprotocol.io/specification/latest — read: lifecycle, transports, tools, resources,
   prompts, sampling, roots, security best practices.
3. **MCP spec GitHub** (schemas + changelog — diff old vs 2026-07-28):
   https://github.com/modelcontextprotocol/modelcontextprotocol
4. **AAIF announcement of the MCPA** (context on the cert program):
   https://aaif.io/blog/introducing-the-mcpa-the-first-official-certification-for-the-model-context-protocol
5. **Official MCP SDKs** (skim one to see the protocol concretely):
   https://github.com/modelcontextprotocol (TypeScript / Python / Go / Kotlin / C#)
6. **Reference servers** (see real tool/resource definitions):
   https://github.com/modelcontextprotocol/servers

## Practice rules

- ✅ facts in this pack are checked against 1–3; anything 🟡 came from third-party sources.
- Do **not** use "exam dumps" sites — against LF exam policy, frequently wrong, and gets candidates banned.
- Best hands-on prep: run one stdio server + one Streamable HTTP server and watch the JSON-RPC traffic
  with a proxy/logger (read the initialize handshake out loud to yourself once).
