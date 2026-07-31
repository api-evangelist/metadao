---
name: Pull MetaDAO Futarchy market data
description: Retrieve DAO trading pairs, real-time pricing, liquidity, and aggregate volume from the MetaDAO Futarchy DEX API.
api: openapi/metadao-futarchy-dex-openapi.yml
operations: [getApiInfo, getTickers, getAggregateVolume]
---

# Pull MetaDAO Futarchy market data

The MetaDAO Futarchy DEX API is a public, read-only, CoinGecko-compatible API. No
authentication is required. Base URL: `https://market-api.metadao.fi`. Respect the
rate limit of **60 requests per minute per IP** and back off on HTTP 429.

## Steps

1. (Optional) Call `getApiInfo` — `GET /` — to confirm the API version and the
   map of available endpoints.
2. Call `getTickers` — `GET /api/tickers` — to list every discovered DAO trading
   pair. Each ticker includes `ticker_id` (`{BASE_MINT}_{QUOTE_MINT}`),
   `base_currency`/`target_currency` mint addresses, `last_price`, `base_volume`,
   `target_volume`, `liquidity_in_usd`, `bid`, and `ask`. Prices are computed from
   spot pool reserves only.
3. Call `getAggregateVolume` — `GET /api/volume/aggregate` — for `totalVolume`,
   a `dailyBreakdown[]` (date/volume/trades), and per-`tokens[]` totals since launch.

## Rules

- Handle errors via the simple `{ "error": "..." }` envelope (not RFC 9457); the
  HTTP status carries the category (400/404/429/500/502/503). See
  `errors/metadao-error-codes.yml`.
- On `429`, wait for the next minute window and retry with exponential backoff;
  cache responses to stay under the limit. See `conventions/metadao-conventions.yml`.
- Symbols may be empty when on-chain metadata is unavailable — key on mint
  addresses, not symbols.
