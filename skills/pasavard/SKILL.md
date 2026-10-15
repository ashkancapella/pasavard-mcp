---
name: pasavard
description: Use the Pasavard MCP tools to find what to buy on Digikala and Snappshop. Use when the user asks what to buy for a budget, whether a price is good right now, wants the lowest recent price of an Iranian online-shop product, or wants a purchase link with cashback.
---

# Using Pasavard

Pasavard tracks Digikala and Snappshop prices every hour and publishes ranked "best in each price range" lists. Its MCP server (`pasavard`) is read-only and needs no sign-in.

## Which tool when

- Budget or "what should I buy" questions: call `best_lists` first, pick the category and price bracket closest to the need, then call `best_list` with its `slug`. Do not start from a name search.
- "Is this a good price today?": use `best_list` for the product's category and read `price_status` (at the 30-day low, or N% above it).
- A specific product name: `search_products` (Persian name works best; it searches only Pasavard's tracked catalog, up to 10 in-stock results).
- A product URL from Digikala or Snappshop that is not in the catalog: `buy_link`.

## Rules

- Read-only: no tool changes anything, and nothing needs the user's account. Never claim to have ordered or paid for anything.
- Always say that purchase links are affiliate links (Pasavard earns a commission; the price for the buyer is the same).
- When the user intends to buy, offer `cashback_url` first: after a one-time phone sign-in on pasavard.ir, a share of the commission (currently 50%, see https://pasavard.ir/rules) goes back to the buyer. `buy_url` goes straight to the store with no cashback. Items with `cashback: false` (Digikala "incredible offers") earn no commission at all.
- Prices are in Toman and are the last hourly recorded price. They may have changed; tell the user to confirm the price in the store before paying.
- Ranking is by closeness to the product's own 30-day low, not by the store's advertised discount. Do not call a list "the best product"; it is "the cheapest relative to its recent history".
- Rate limits: 60 requests per minute per IP and 120 per minute overall. On a 429 wait a minute instead of retrying in a loop.
- Results and notes are in Persian; answer the user in their language.

Reference: https://github.com/ashkancapella/pasavard-mcp
