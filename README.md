# mcp-gurufocus

GuruFocus MCP — wraps the GuruFocus Data API (gurufocus.com/data-api)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1856+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

| Tool | Description |
|------|-------------|
| `gurufocus_summary` | Get a GuruFocus stock summary/overview for a symbol — company info plus proprietary rankings including GF Score (0-100), GF Value (fair-value estimate), and sub-rankings (financial strength, profitability, growth, momentum, predictability). Example: gurufocus_summary({ symbol: "AAPL", _apiKey: "your-token" }) |
| `gurufocus_gurus` | Get guru holding-change records for a symbol — which famous investors (gurus) bought or sold. Returns guru name, action, share-change signal, current shares, portfolio date, and position weight. Example: gurufocus_gurus({ symbol: "AAPL", _apiKey: "your-token" }) |
| `gurufocus_insider` | Get insider trades for a symbol — corporate insiders buying/selling. Returns insider identity, role/position, transaction date, trade type, share count, execution price, and resulting ownership. Supports pagination. Example: gurufocus_insider({ symbol: "AAPL", page: 1, _apiKey: "your-token" }) |
| `gurufocus_price` | Get historical OHLCV price bars for a symbol — open, high, low, close, volume time series. Example: gurufocus_price({ symbol: "AAPL", _apiKey: "your-token" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "gurufocus": {
      "url": "https://gateway.pipeworx.io/gurufocus/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/gurufocus/mcp` returns the tools in the table
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

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/gurufocus_summary`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-gurufocus` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/gurufocus/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "gurufocus": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-gurufocus"]
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
ask_pipeworx({ question: "your question about Gurufocus data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
