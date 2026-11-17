# StocksScores MCP server

A remote [Model Context Protocol](https://modelcontextprotocol.io) server that gives AI assistants a
0–100 score for any of ~4,100 US-listed stocks — and the reasons behind it.

Each score blends a **fundamental pillar** (valuation, growth, profitability, balance sheet and analyst
revisions, judged against sector ranges) with a **technical pillar** across timeframes from 5-minute to
quarterly bars, weighted 80 / 20. News sentiment is reported but not counted. The engine runs 120
scorers and rescores the whole universe every night.

```
https://api.stocksscores.com/mcp
```

Streamable HTTP, plain JSON responses. **No API key, no account, nothing to install.**

This repository holds the documentation and the registry manifest; the server itself is hosted.

## Connect

**Claude Code**

```sh
claude mcp add --transport http stocksscores https://api.stocksscores.com/mcp
```

Add `--scope user` to make it available in every project.

**Claude Desktop / claude.ai** — Settings → Connectors → Add custom connector. Name it StocksScores, paste
the URL, leave the OAuth fields empty.

**Cursor** (`~/.cursor/mcp.json`) and other clients that read an `mcpServers` file:

```json
{
  "mcpServers": {
    "stocksscores": { "type": "http", "url": "https://api.stocksscores.com/mcp" }
  }
}
```

**VS Code** (`.vscode/mcp.json` — the key is `servers`):

```json
{
  "servers": {
    "stocksscores": { "type": "http", "url": "https://api.stocksscores.com/mcp" }
  }
}
```

**Clients that only run local (stdio) servers** — bridge with [`mcp-remote`](https://www.npmjs.com/package/mcp-remote):

```json
{
  "mcpServers": {
    "stocksscores": { "command": "npx", "args": ["-y", "mcp-remote", "https://api.stocksscores.com/mcp"] }
  }
}
```

**Test from a terminal** (listing tools is not metered):

```sh
curl -s https://api.stocksscores.com/mcp -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

## Tools

| Tool | What it returns | Freshness |
| --- | --- | --- |
| `get_stock_analysis` | One symbol: the composite 0–100, its three pillars, the technical score per timeframe, every sub-score, the current quote | Live, cached 2 h |
| `screen_stocks` | Filter last night's scores by `minScore` / `maxScore` / `sector`, or compare up to 50 named `symbols` in one call — compact rows, best first (max 200), each with its 7-day score change | Nightly |
| `top_scores` | The highest-scoring investable names right now | Nightly |
| `get_score_history` | One symbol's nightly score for up to 365 days, with its pillars, the price the batch saw, and the 7- and 30-day change | Nightly |

With a personal API key (below) the server also lists eight account tools: `list_alerts`, `create_alert`,
`update_alert`, `delete_alert` (email price alerts) and `list_watchlists`, `create_watchlist`,
`update_watchlist`, `delete_watchlist`.

Every score is 0–100, higher is more bullish: 0–39 bearish, 40–49 weak, 50–65 neutral, 66–79 bullish,
80+ very bullish. Missing data is left out of a score, never counted as zero.

## Limits and keys

| | Tool calls |
| --- | --- |
| Anonymous (per IP), `get_stock_analysis` — computed live | 1 a minute, 10 a day |
| Anonymous (per IP), every other tool — nightly reads | 10 a minute, 100 a day |
| Personal API key | 30 a minute, 500 a day |

Only `tools/call` is metered — the handshake and `tools/list` are free. A key is free: sign in at
[app.stocksscores.com/developers](https://app.stocksscores.com/developers), create one, and send it as
`Authorization: Bearer ss_…`:

```sh
claude mcp add --transport http stocksscores https://api.stocksscores.com/mcp \
  --header "Authorization: Bearer ss_your_key"
```

A key can read and manage your own alerts and watchlists; it cannot touch billing or your account.

## More

- Full guide, example prompts and comparisons: [stocksscores.com/posts/43](https://stocksscores.com/posts/43/)
- The same data over a keyless REST API: [stocksscores.com/posts/46](https://stocksscores.com/posts/46/)
- Every scored stock, with its breakdown: [stocksscores.com/stocks](https://stocksscores.com/stocks/)
- Embeddable score badges: [stocksscores.com/widgets](https://stocksscores.com/widgets/)

[![AAPL stock score](https://stocksscores.com/stocks/aapl/badge.svg)](https://stocksscores.com/stocks/aapl/)
[![NVDA stock score](https://stocksscores.com/stocks/nvda/badge.svg)](https://stocksscores.com/stocks/nvda/)
[![MSFT stock score](https://stocksscores.com/stocks/msft/badge.svg)](https://stocksscores.com/stocks/msft/)

Scores are information, not investment advice. Past scores do not predict future returns.
Questions: support@stocksscores.com
