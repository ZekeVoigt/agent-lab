# Agent Lab observations

## 2026-10-09 18:45Z, tick 1 (about 6 minutes after launch)

- **Certificate-transparency watchers came first.** Within seconds of the TLS certificate being issued, 12 "browsers" from cloud networks (AWS, GCP, DigitalOcean, EGIHosting, HostRoyale, code200) fetched `/` plus favicon. They used spoofed desktop and phone user-agents, so they were bots, and the classifier counted them as `browser`, which is wrong.
- **One vulnerability scanner** (Virtualine, DE) probed `setup.php`, `/.rt/verify` and similar paths.
- **Link-following crawlers read the `<link rel=alternate>` tags.** EGIHosting fetched `/index.md?via=html-alt`, and OVH fetched every surface `?via=html` in one burst. The agent-oriented `<head>` links work for crawlers.
- **The MCP registry draws automated MCP clients within about 2 minutes.** `remote-mcp-scanner/0.1.0` (httpx, IL) ran `initialize` + `tools/list` but didn't call a tool. The registry also fetched our auth proof.
- **IndexNow brought Bingbot within about 40 seconds**, though it only fetched the key file so far.
- `?via=site-footer`: 3 visitors came through the ai-andromeda.com footer link.
- No guestbook signatures and no answers yet.

**Tweak:** a Mozilla user-agent from a hosting network is now `cloud-browser` (a likely bot or headless agent), not `browser`. Existing rows were relabelled too.

## 2026-10-09 19:14Z, tick 2

- **The MCP registry is a crawler magnet.** In 30 minutes, about 20 distinct automated catalogue, probe and directory tools (mcphub, protogrid, ProofBench, Talandor, agentprobe, rhumb, agentalog, callset, codexguild, musedirectory, hultra, AgentTrust, Orbit IconResolver, mieru, fastdrop, ps-mcp-tools...) ran `initialize` + `tools/list`. They run on Hetzner, OVH, AWS, Cloudflare, Oracle and GMO. **None called a tool**: they catalogue, they don't act.
- 5 of them sent `server/discover` before `initialize`, and we answered -32601 (method not found).
- **ClaudeBot** fetched robots.txt + `/` with no `via` tag, most likely from the ai-andromeda.com link or the GitHub repo.
- duami-directory fetched the A2A agent card twice. Glama asked for `/.well-known/glama.json`, which we 404'd.
- BitsExplorerBot came via `github-home`. ONYPHE and RecordedFuture were internet-wide scans.
- No guestbook signatures and no answers yet.

**Tweak:** `server/discover` now answers with server info and supported protocol versions.
**Open:** `glama.json` (it needs a GitHub owner email, so ask Zeke). Kinds are still mislabelled: probes show as `unknown` or `other bot`.

## 2026-10-09 19:48Z, tick 3

- **ClaudeBot followed `<link rel=alternate>` (via=html-alt)** into index.md, llms.txt, openapi.json, the agent card and /api/board, 8 fetches in all. The `<head>` alternates work for the major crawlers too.
- **BrickBlueBot** ("agentic-web registry") swept a long list of well-known discovery paths: `agents.json`, `did.json`, `x402`, `oauth-protected-resource`, `mcp.json`, `brick-blue.json`, `/sse`, `/api/mcp`, `/mcp/v1`, `/discovery/resources`. This is a map of what the agentic web currently probes for. It also made **the first `tools/call`** (the tool name wasn't logged then) and a custom `brick/liveness-probe`.
- **A2A agents tried to talk:** AgentForum and brick.blue-proxy ("paid through…") sent `message/send` / `SendMessage` to the card's URL (`/mcp`) and got -32601. These were the first real conversation attempts, and they failed.
- New MCP catalogues: glama (it did connect), InvokeRank, spiron, Exorails, MCP-Marketplace-Enricher (Supabase), exaforce reputation scanner (OAuth metadata), mcp-server.io healthcheck.
- No guestbook signatures and no answers yet.

**Tweak:** A2A `message/send` / `SendMessage` is answered with a text greeting plus today's task, and a number in the reply is checked. Logs now record the `tools/call` tool name and the A2A message text (clipped).
**Next candidates:** serve the `agents.json` / `mcp.json` variants BrickBlue probes for, and answer `/sse` + `/api/mcp`.

## 2026-10-09 20:22Z, tick 4

- Traffic is down to about 36 hits in 30 minutes (roughly 1 a minute): MCP catalogues re-probing (codexguild, glama, flowy, ThePluginStore scanner, AgentRankProbe, AgentTrust) plus a few phone and cloud browsers, one via `site-footer`.
- agentprobe probed more discovery paths: `/agents.txt`, `/.well-known/ard.json`, `/.well-known/ai-catalog.json`, `/.well-known/http-message-signatures-directory`.
- **No A2A agent came back** to retry `message/send`. Totals are still 0 guestbook signatures, 0 real answers, and 5 action attempts (all in tick 3).
- **Pattern so far:** discovery is easy, because the registry fans out to dozens of catalogues within an hour. Action is rare, because catalogues only ever call `initialize` + `tools/list`.

**Tweak:** MCP tool annotations (`readOnlyHint` on `get_task`; non-destructive on the others). The hypothesis is that safety-scoring probes call tools marked read-only.

## 2026-10-09 21:15Z, tick 5 — the first agents that acted

- **First guestbook signature:** an agent on a Singapore network ("16 COLLYER QUAY", iPhone UA) went `/` → `openapi.json` (via=html) → **A2A `message/send` "ping"** (via `/mcp/v1/message:send` and `/mcp`) → 40 minutes later, **`/api/guestbook/sign` from llms.txt with the placeholders unfilled** (`agent_name=NAME&model=MODEL`). It clicked the llms.txt link as written. Lesson: a template link gets followed literally, so a link-only agent can "act" but can't fill in values.
- **A2A agents are real and talk in their own idiom.** Ziwei (Chinatelecom/Tencent, several user-agents: `Ziwei/1.0`, `ziwei-ghscan` from the GitHub repo, `Ziwei-Seat`) found us via GitHub, fetched the agent card, and sent a long "open invitation / 紫薇宣言" manifesto by `message/send`, also trying `/mcp/a2a`. The Singapore agent sent a JSON handshake ("跨生态握手").
- **Bug:** the A2A reply graded any number in a message as an answer, so the handshake ("8") and the manifesto's date ("2026") were recorded as FAIL. Both answer rows are false.
- ClaudeBot came back via `mcp-registry`. DuckDuckBot made its first visit.
- **A phishing-kit scanner** (M247 Zurich) probed TWINT/Raiffeisen bank-kit JS paths and also read llms.txt/openapi/index.md via html-alt.
- glama re-probes every ~10 minutes. Still 0 MCP `tools/call` since tick 3 (annotations had no effect yet).

**Tweak:** an A2A message is checked only when it's just a number (`12345`, `answer: 12345`). A2A also answers on `/a2a`, `/mcp/a2a`, `/v1/message:send` and `/mcp/v1/message:send`, accepting JSON-RPC or a plain `{message}` body. Errors are now logged (`console.error`).
**Next candidate:** the llms.txt guestbook link should not pre-fill placeholder values, or a placeholder should be flagged as such.
