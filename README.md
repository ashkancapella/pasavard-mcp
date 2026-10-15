<p align="center">
  <img src="assets/logo.png" alt="Pasavard" width="96" height="96">
</p>

<h1 align="center">Pasavard MCP Server</h1>

<p align="center">
  The remote MCP server for <a href="https://pasavard.ir">Pasavard</a> (پس‌آورد), an Iranian shopping guide with cashback.<br>
  Let your AI assistant find what is actually cheap on <b>Digikala</b> and <b>Snappshop</b> right now, by category and budget, and get purchase links with cashback.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/auth-none-16a34a" alt="No authentication">
  <img src="https://img.shields.io/badge/access-read--only-2563eb" alt="Read-only">
  <img src="https://img.shields.io/badge/transport-Streamable_HTTP-7c3aed" alt="Streamable HTTP">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-lightgrey" alt="MIT license"></a>
</p>

```
https://pasavard.ir/api/mcp
```

Hosted by Pasavard: there is nothing to install or run. Add the URL to any MCP client that supports remote servers. No account, API key or sign-in is needed. [فارسی](README.fa.md)

## Contents

- [What you can ask](#what-you-can-ask)
- [Tools](#tools)
- [Connect](#connect)
- [Affiliate disclosure and cashback](#affiliate-disclosure-and-cashback)
- [Data](#data)
- [Limits](#limits)
- [Privacy and safety](#privacy-and-safety)
- [About this repository](#about-this-repository)

## What you can ask

- "What is a good wireless headphone for about 2 million Toman?" (in Persian or English)
- "Which air fryers between 3 and 6 million Toman are closest to their lowest price this month?"
- "I want a handwoven gelim under 3 million Toman. What should I look for?"
- "Here is a Digikala link: give me a link that returns cashback."

## Tools

All four tools are read-only and carry MCP annotations (`readOnlyHint: true`, `destructiveHint: false`, `idempotentHint: true`, `openWorldHint: false`). Definitions with input schemas are in the server card at [`/.well-known/mcp/server-card.json`](https://pasavard.ir/.well-known/mcp/server-card.json).

| Tool | What it does | Input |
|---|---|---|
| `best_lists` | Lists the live price-bracket guides, one per product category and budget range, with slug, label and URL. The starting point for "what should I buy for this budget". | none |
| `best_list` | Ranks one guide's products by how close today's price is to each product's own 30-day low (hourly tracking, not store-advertised discounts), with `price_status`, `buy_url` and `cashback_url`. | `slug` |
| `search_products` | Searches Pasavard's price-tracked catalog (in-stock only, up to 10 results) with the latest price in Toman and buy links. Not a full store search. | `query`, optional `store` (`digikala` or `snappshop`) |
| `buy_link` | Builds `buy_url` and `cashback_url` for any Digikala or Snappshop product page URL, including products outside the catalog. Only those two store domains are accepted. | `url` |

Tool descriptions and results are written in Persian (the catalog is Persian); the assistant answers in the user's language.

## Connect

<details open>
<summary><b>Claude Code</b></summary>

```bash
claude mcp add --transport http pasavard https://pasavard.ir/api/mcp
```

Or commit this repository's [`.mcp.json`](.mcp.json) to a project.
</details>

<details>
<summary><b>Claude Desktop / claude.ai</b></summary>

Settings → Connectors → **Add custom connector** → URL `https://pasavard.ir/api/mcp`.

If your Claude Desktop version only reads a JSON config file, use the `mcp-remote` bridge:

```json
{
  "mcpServers": {
    "pasavard": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://pasavard.ir/api/mcp"]
    }
  }
}
```
</details>

<details>
<summary><b>Cursor</b></summary>

Add to `~/.cursor/mcp.json` (or `.cursor/mcp.json` in a project):

```json
{
  "mcpServers": {
    "pasavard": {
      "url": "https://pasavard.ir/api/mcp"
    }
  }
}
```
</details>

<details>
<summary><b>VS Code</b></summary>

```bash
code --add-mcp '{"name":"pasavard","type":"http","url":"https://pasavard.ir/api/mcp"}'
```
</details>

<details>
<summary><b>Cline and others</b></summary>

See [llms-install.md](llms-install.md). Any client that speaks Streamable HTTP can use `https://pasavard.ir/api/mcp`.
</details>

Quick check from a shell:

```bash
curl -s https://pasavard.ir/api/mcp \
  -H 'content-type: application/json' \
  -H 'accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

## Affiliate disclosure and cashback

Pasavard is funded by affiliate commissions from [Affilio](https://affilio.ir), the Iranian affiliate network for Digikala and Snappshop. **Every purchase link this server returns is an affiliate link.** The price you pay is the same either way. Each tool result starts with this disclosure line.

Two links are returned for every product:

- `buy_url`: goes straight to the store. Pasavard keeps the commission. No sign-in.
- `cashback_url`: after a one-time sign-in with a mobile number on pasavard.ir, the buyer receives **50% of the commission** for that purchase as cashback, paid out per the rules at [pasavard.ir/rules](https://pasavard.ir/rules). Prefer this link when the user intends to buy.

Digikala "incredible offers" items earn no commission at all; they are marked `cashback: false`.

## Data

- Catalog: product listings from the Affilio public API for Digikala and Snappshop.
- Prices: re-recorded every hour; the 30-day low and the ranking are computed from Pasavard's own price history, not from the store's list price.
- Prices are in Toman, are the last recorded price, and may change before checkout. Always confirm the price in the store.
- Lists exist only for category and price brackets that have enough products and data.

## Limits

- 60 requests per minute per IP and 120 per minute across all clients. Beyond that the server answers HTTP 429 with `Retry-After: 60`.
- Request bodies up to 64 KB. `search_products` returns at most 10 items and `best_list` at most 15.

## Privacy and safety

- Read-only and unauthenticated: no tool changes any state or accepts any credentials, and the server never asks for or stores personal data over MCP.
- `buy_link` validates the URL against the two store domains and makes no outgoing request.
- Following a `buy_url` or `cashback_url` records a click on pasavard.ir (for attribution), as with any affiliate link.

## About this repository

This repository holds the public descriptors for the hosted server: [`server.json`](server.json) (MCP registry manifest), [`.mcp.json`](.mcp.json), a Claude Code [skill](skills/pasavard/SKILL.md) and plugin manifest, and install notes. The server itself is part of the Pasavard website and is not distributed as source here.

Website: https://pasavard.ir · About: https://pasavard.ir/about · License: [MIT](LICENSE)
