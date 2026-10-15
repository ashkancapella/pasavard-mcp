# Installing the Pasavard MCP server

Pasavard's MCP server is **remote**: there is nothing to clone, build or run. Add one entry to the client's MCP settings. There is no sign-in, API key or environment variable.

## Cline

Add this to `cline_mcp_settings.json` (MCP Servers → Configure MCP Servers):

```json
{
  "mcpServers": {
    "pasavard": {
      "type": "streamableHttp",
      "url": "https://pasavard.ir/api/mcp",
      "disabled": false,
      "autoApprove": ["best_lists", "best_list", "search_products", "buy_link"]
    }
  }
}
```

All four tools are read-only, so auto-approving them is safe. Leave `autoApprove` empty if you prefer to see each call.

## Other clients

- Claude Code: `claude mcp add --transport http pasavard https://pasavard.ir/api/mcp`
- Cursor: `{"mcpServers": {"pasavard": {"url": "https://pasavard.ir/api/mcp"}}}`
- VS Code: `code --add-mcp '{"name":"pasavard","type":"http","url":"https://pasavard.ir/api/mcp"}'`
- Anything else: a remote Streamable HTTP server at `https://pasavard.ir/api/mcp`

## Check it works

Ask: "What can I buy for about 2 million Toman in wireless headphones?" The assistant should call `best_lists`, then `best_list`.

## Things to relay to the user

- Product links are affiliate links (Affilio, for Digikala and Snappshop). The price is the same either way.
- `cashback_url` returns a share of the commission to the buyer after one phone sign-in on pasavard.ir; `buy_url` does not.
- Prices are in Toman, are the last recorded hourly price, and can change before checkout.
- Rate limits: 60 requests per minute per IP, 120 per minute overall.

Full description: https://github.com/ashkancapella/pasavard-mcp and https://pasavard.ir/about
