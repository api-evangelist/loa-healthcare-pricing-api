---
name: find-entity-prices
description: Find a hospital or provider on Loa and retrieve its source-labeled prices for specific CPT/HCPCS codes.
api: Loa Healthcare Pricing API
operations: [searchEntities, getEntityPrices]
---

# Find source-labeled prices for one hospital or provider

1. `GET /api/v1/entities/search` (**searchEntities**) with `q` (name), optional `type`
   (`hospital` | `provider`), `state` (two-letter code or full name — unknown states return
   `InvalidEntitySearch`), `limit` (1-50). Public — no API key needed. Take the `slug` from
   `results[]`.
2. `GET /api/v1/entities/{slug}/prices` (**getEntityPrices**) with optional `cpt_code`
   (repeat or comma-separate, max 25 codes) and `limit` (1-100).
3. Read every row's `provenance` before presenting a price: `authority`, `source_label` and
   `confidence` distinguish Published MRF prices from Loa Verified Provider Price overlays;
   `caveats` list limitations. A reviewed provider price may carry `comparableMrfPrice` — show both.
4. Errors arrive as `{request_id, error: {code, message}}`: `EntityNotFound` means use an exact Loa
   slug from step 1; `ApiRateLimited` (429) means back off — no rate-limit headers are returned.
