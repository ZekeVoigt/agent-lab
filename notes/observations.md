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
