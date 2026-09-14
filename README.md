# mcp-marketdata-app

Market Data App MCP — wraps the Market Data App API (marketdata.app)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1573+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `marketdata_option_chain` | Get the full option chain for an underlying stock/ETF/index, with strike, bid/ask/mid/last, volume, open interest, IV and greeks (delta/gamma/theta/vega). Filter by expiration, days-to-expiration, delta, side, and liquidity. Example: marketdata_option_chain({ underlyingSymbol: "AAPL", expiration: "2026-01-16", side: "call", minOpenInterest: 100, _apiKey: "your-key" }) |
| `marketdata_option_quote` | Get a live/delayed quote plus greeks for a single option contract, identified by its OCC option symbol. Returns bid/ask/mid/last, volume, open interest, IV, delta/gamma/theta/vega, and intrinsic/extrinsic value. Example: marketdata_option_quote({ optionSymbol: "AAPL260116C00150000", _apiKey: "your-key" }) |
| `marketdata_stock_quote` | Get a live/delayed stock quote: bid/ask/mid/last, absolute + percent change, and session volume. Example: marketdata_stock_quote({ symbol: "AAPL", _apiKey: "your-key" }) |
| `marketdata_candles` | Get historical OHLCV candles for a stock symbol at a given resolution ("D" daily, "H" hourly, "5" for 5-minute, etc.). Provide a from/to date range. Example: marketdata_candles({ resolution: "D", symbol: "AAPL", from: "2026-01-01", to: "2026-06-30", _apiKey: "your-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "marketdata-app": {
      "url": "https://gateway.pipeworx.io/marketdata-app/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/marketdata-app/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1573+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "marketdata-app": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-marketdata-app"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-marketdata-app
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Marketdata App data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
