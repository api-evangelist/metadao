---
name: Look up MetaDAO token supply
description: Retrieve total, circulating, and fully-broken-down supply for a MetaDAO launchpad token by its Solana mint address.
api: openapi/metadao-futarchy-dex-openapi.yml
operations: [getSupply, getTotalSupply, getCirculatingSupply]
---

# Look up MetaDAO token supply

Public, read-only API. Base URL: `https://market-api.metadao.fi`. No auth required;
rate limit 60 req/min per IP.

## Inputs

- `mintAddress` — the SPL token mint address (Solana PublicKey), e.g.
  `METAwkXcqyXKy1AtsSgJ8JiUHwGCafnZL38n3vYmeta`.

## Steps

1. For the full picture, call `getSupply` — `GET /api/supply/{mintAddress}` — which
   returns `result` (formatted total supply) and a `data` object with the allocation
   breakdown: circulating supply, team performance package, FutarchyAMM liquidity,
   and Meteora LP liquidity.
2. For just the headline number, call `getTotalSupply` — `GET /api/supply/{mintAddress}/total`.
3. For circulating supply, call `getCirculatingSupply` — `GET /api/supply/{mintAddress}/circulating`.

## Rules

- For launchpad tokens, **circulating supply excludes** the team performance
  package (price-based unlock) but **includes** all liquidity (both FutarchyAMM
  and Meteora DAMM), since those tokens are tradeable.
- A `404` means the mint/DAO was not found — validate the mint address.
- Handle `429` with backoff; see `conventions/metadao-conventions.yml` and
  `errors/metadao-error-codes.yml`.
