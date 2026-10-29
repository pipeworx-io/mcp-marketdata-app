# mcp-marketdata-app

Market Data App MCP — wraps the Market Data App API (marketdata.app)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1856+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

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

Both URLs reach the same gateway and the same 1856+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/marketdata_option_chain`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-marketdata-app` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/marketdata-app/mcp` endpoint shown above. It needs network access;
there is no offline mode.

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

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

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
