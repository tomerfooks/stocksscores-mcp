# AGENTS.md — stocksscores-mcp

Public, docs-only repo for the hosted StocksScores MCP server (`https://api.stocksscores.com/mcp`).
The server code lives in the private `stocksscores-server` repo (`routes/mcp/index.ts`); nothing here runs.

- `README.md` — what directories and users read. Keep it in step with the server: tool names, the
  anonymous / keyed limits (meters in `routes/mcp/index.ts`), and the setup snippets in post 43.
- `server.json` — the official MCP Registry manifest, published as `com.stocksscores/stocksscores`.
  To change the listing: bump `version`, then
  ```sh
  K=~/.config/mcp-publisher/stocksscores-ed25519.pem
  mcp-publisher login dns --domain stocksscores.com --private-key "$(openssl pkey -in $K -outform DER | tail -c 32 | xxd -p -c 64)"
  mcp-publisher validate && mcp-publisher publish
  ```
  The DNS proof is the `v=MCPv1; k=ed25519; p=…` TXT record on stocksscores.com (Cloudflare). The
  private key exists only on the owner's machine; if it is lost, generate a new pair and replace the TXT.
- Listings that point here: official MCP Registry (feeds PulseMCP, VS Code, GitHub), mcp.so
  (chatmcp/mcpso#7347). Glama and awesome-remote-mcp-servers need the owner's Glama sign-in first.
