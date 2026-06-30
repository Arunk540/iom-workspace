---
category: runtime
title: Diesel price range query flow
summary: How the diesel endpoint normalizes parameters and chooses either compatibility or paired-key lookup semantics.
primary_for: [diesel-query-flow]
mentions: [diesel, country-code, customer-code, read-only, compatibility]
scenarios:
  - trace diesel endpoint
  - inspect diesel validation
  - see country customer rule
  - find diesel query
  - follow slab lookup
capabilities: [runtime-flow]
domains: [diesel-price-ranges]
entities: [DieselPriceRangesController, DieselPriceRangesServiceImpl]
sources:
  - diesel-price-ranges/diesel-price-ranges-controller/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/controller/DieselPriceRangesController.kt
  - diesel-price-ranges/diesel-price-ranges-controller/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/controller/service/DieselPriceRangesServiceImpl.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - domain/diesel-price-ranges.md
  - contracts/db-schemas.md
  - navigation/scenarios.md
---

# Flow

1. `GET /diesel-price-ranges` normalizes blank values and the string `NULL` to null before deciding query behavior. (source: diesel-price-ranges/diesel-price-ranges-controller/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/controller/DieselPriceRangesController.kt:21)
2. When both parameters are absent, the controller preserves backward compatibility by defaulting `countryCode` to `VN`. (source: diesel-price-ranges/diesel-price-ranges-controller/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/controller/DieselPriceRangesController.kt:32)
3. When only one of `countryCode` or `customerCode` is supplied, the controller raises `400 BAD_REQUEST`. (source: diesel-price-ranges/diesel-price-ranges-controller/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/controller/DieselPriceRangesController.kt:38)
4. Valid paired requests delegate to `DieselPriceRangesServiceImpl.getByCountryCodeAndCustomerCode`, which maps repository rows and raises `NotFoundException` on an empty result. (source: diesel-price-ranges/diesel-price-ranges-controller/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/controller/DieselPriceRangesController.kt:47; diesel-price-ranges/diesel-price-ranges-controller/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/controller/service/DieselPriceRangesServiceImpl.kt:15)
