---
name: compare-procedure-prices
description: Compare source-labeled prices for CPT codes across up to 10 Loa entities.
api: Loa Healthcare Pricing API
operations: [searchEntities, comparePrices]
---

# Compare procedure prices across entities

1. Resolve slugs with `GET /api/v1/entities/search` (**searchEntities**) — one call per candidate
   hospital/provider, or filter by `state`.
2. `GET /api/v1/prices/compare` (**comparePrices**) with `entity` (comma-separated slugs, max 10)
   and `cpt_code` (comma-separated, max 10 — 5-digit CPT or letter+4-digit HCPCS). Optional
   `limit_per_entity` (1-25). Both `entity` and `cpt_code` are required; violations return
   `InvalidPriceCompareRequest` (400).
3. `comparisons[]` groups rows by CPT code and entity. Compare like with like using `priceKind`
   (`cash`, `discounted_cash`, `package_cash`, `payer_negotiated`) and check `provenance.confidence`
   — do not average a payer-negotiated rate against a cash price.
4. This is a read-only GET; safe to retry. 429 `ApiRateLimited` means back off.
