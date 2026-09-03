---
name: submit-price-correction
description: Submit a reviewed price or profile correction for a hospital or provider to Loa.
api: Loa Healthcare Pricing API
operations: [searchEntities, submitEntityUpdate]
---

# Submit a reviewed correction

1. Identify the entity first via `GET /api/v1/entities/search` (**searchEntities**) and carry its
   `id` (as `entity_page_id`) or `slug` (as `entity_slug`).
2. `POST /api/v1/entity-updates` (**submitEntityUpdate**). Required: `submitter_email`,
   `request_type` (`price_update` | `profile_correction` | `new_listing` | `remove_listing` |
   `other`). Strongly include: `source_urls` (max 10 public URLs reviewers can verify),
   `attestation` (`provider_representative` | `authorized_agent` | `public_source` | `other`), and
   `price_items[]` (max 100; each needs a `cpt_code` matching `^(\d{5}|[A-Za-z]\d{4})$`, prices in
   integer cents via `proposed_price_cents`).
3. Idempotency: pass a stable `idempotency_key` (8-200 chars) when retrying — Loa derives one from
   the payload if omitted. A replay returns **200** with `reused_existing: true`; a fresh request
   returns **201**.
4. The submission only creates a PENDING review request — public data never changes until Loa staff
   approve it, and the API offers no way to withdraw a submitted request, so send the smallest
   verifiable change. `UpdateRequestValidationFailed` (400) lists what to fix.
