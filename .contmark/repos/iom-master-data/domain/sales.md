---
category: domain
title: Sales reference data
summary: Sales exposes read-oriented APIs for sales type search and sales object matching against persisted lookup tables.
primary_for: [sales-type-reference]
mentions: [sales-types, sales-objects, read-only, matching]
scenarios:
  - find sales endpoint
  - trace sales object lookup
  - see sales tables
  - inspect read-only flow
  - find sales service
capabilities: [domain-summary]
domains: [sales]
entities: [SalesTypesController, SalesTypesServiceImpl, SalesTypeEntity, SalesObjectEntity]
sources:
  - sales/sales-controller/src/main/kotlin/com/maersk/iom/master/data/sales/controller/controller/SalesTypesController.kt
  - sales/sales-controller/src/main/kotlin/com/maersk/iom/master/data/sales/controller/service/SalesTypesServiceImpl.kt
  - sales/sales-persistence/src/main/kotlin/com/maersk/iom/master/data/persistence/SalesTypeEntity.kt
  - sales/sales-persistence/src/main/kotlin/com/maersk/iom/master/data/persistence/SalesObjectEntity.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - runtime/sales-object-lookup-flow.md
  - contracts/db-schemas.md
  - navigation/scenarios.md
---

# Scope

- `GET /sales-types` searches cached or freshly loaded IFS sales types.
- `POST /sales-objects` matches each request container to a persisted sales object row.
- The module is explicitly read-only in intent, even though it accepts POST for lookup input.
