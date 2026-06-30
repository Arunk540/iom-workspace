---
category: runtime
title: Sales object lookup flow
summary: How sales type search and sales object matching are served from reactive repositories and an in-memory cache.
primary_for: [sales-object-lookup-flow]
mentions: [sales, lookup, matching, cache, read-only]
scenarios:
  - trace sales object match
  - see sales type cache
  - inspect sales lookup
  - find sales service
  - follow sales request
capabilities: [runtime-flow]
domains: [sales]
entities: [SalesTypesController, SalesTypesServiceImpl, SalesObjectRepository]
sources:
  - sales/sales-controller/src/main/kotlin/com/maersk/iom/master/data/sales/controller/controller/SalesTypesController.kt
  - sales/sales-controller/src/main/kotlin/com/maersk/iom/master/data/sales/controller/service/SalesTypesServiceImpl.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - domain/sales.md
  - contracts/db-schemas.md
  - navigation/scenarios.md
---

# Flow

1. `GET /sales-types` forwards an optional search string to `SalesTypesServiceImpl.getSalesTypes`. (source: sales/sales-controller/src/main/kotlin/com/maersk/iom/master/data/sales/controller/controller/SalesTypesController.kt:19)
2. The service serves from an in-memory `salesTypes` map when available, otherwise reloads all `ifs_sales_types` rows from the repository and refreshes the cache. (source: sales/sales-controller/src/main/kotlin/com/maersk/iom/master/data/sales/controller/service/SalesTypesServiceImpl.kt:21; sales/sales-controller/src/main/kotlin/com/maersk/iom/master/data/sales/controller/service/SalesTypesServiceImpl.kt:62)
3. `POST /sales-objects` sends the request body to `findMatchingSalesObjects`, which looks up each container independently by city, direction, zone, container size, and material number. (source: sales/sales-controller/src/main/kotlin/com/maersk/iom/master/data/sales/controller/controller/SalesTypesController.kt:26; sales/sales-controller/src/main/kotlin/com/maersk/iom/master/data/sales/controller/service/SalesTypesServiceImpl.kt:38)
4. The service zips the per-container lookups back into a single `SalesObject` response, preserving containers with no match. (source: sales/sales-controller/src/main/kotlin/com/maersk/iom/master/data/sales/controller/service/SalesTypesServiceImpl.kt:47)
