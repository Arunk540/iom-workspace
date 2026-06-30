---
category: domain
title: Diesel price range bands
summary: Diesel price ranges exposes a single read API for country/customer-specific slab records used by downstream integrations.
primary_for: [diesel-price-range-reference]
mentions: [diesel-price-ranges, country-code, customer-code, slabs]
scenarios:
  - find diesel endpoint
  - trace diesel query
  - see diesel table
  - inspect solar lookup
  - find diesel service
capabilities: [domain-summary]
domains: [diesel-price-ranges]
entities: [DieselPriceRangesController, DieselPriceRangesServiceImpl, DieselPriceRangesEntity]
sources:
  - diesel-price-ranges/diesel-price-ranges-controller/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/controller/DieselPriceRangesController.kt
  - diesel-price-ranges/diesel-price-ranges-controller/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/controller/service/DieselPriceRangesServiceImpl.kt
  - diesel-price-ranges/diesel-price-ranges-persistence/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/persistence/entity/DieselPriceRangesEntity.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - runtime/diesel-price-range-query-flow.md
  - contracts/db-schemas.md
  - navigation/scenarios.md
---

# Scope

- A single `GET /diesel-price-ranges` endpoint serves both legacy default-country access and paired country/customer lookups.
- Persistence is entirely through `DieselPriceRangesRepository` and the `diesel_price_ranges` table.
