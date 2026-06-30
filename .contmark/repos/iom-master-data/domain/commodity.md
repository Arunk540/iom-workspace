---
category: domain
title: Commodity and HS reference data
summary: Commodity serves commodity and HS code queries, and can filter commodity sets through the Business Rules restriction service.
primary_for: [commodity-data-api]
mentions: [commodities, hs-codes, restrictions, startup-cache]
scenarios:
  - find commodity route
  - trace restriction flow
  - see hs table
  - inspect commodity cache
  - find business rules client
capabilities: [domain-summary]
domains: [commodity]
entities: [CommodityController, CommodityServiceImpl, CommodityByCodeEntity, HSCommodityByCodeEntity]
sources:
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/CommodityController.kt
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/service/CommodityServiceImpl.kt
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/startup/CommodityLoadService.kt
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/webclient/businessrule/CommodityRestrictionClient.kt
  - commodity/commodity-persistence/src/main/kotlin/com/maersk/iom/master/data/commodity/persistence/CommodityByCodeEntity.kt
  - commodity/commodity-persistence/src/main/kotlin/com/maersk/iom/master/data/commodity/persistence/HSCommodityByCodeEntity.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - runtime/commodity-restrictions-flow.md
  - integrations/business-rules-service.md
  - contracts/db-schemas.md
---

# Scope

- `CommodityController` owns commodity search, HS lookups, and non-restricted commodity queries.
- `CommodityLoadService` preloads commodity and HS records at startup for memory-backed reads.
- Restriction decisions come from `CommodityRestrictionClient`; the module does not decide trade compliance itself.

## Data model

- `CommodityByCodeEntity` stores Maersk commodity code, name, cargo types, grouping, coding system, and US-flag indicator.
- `HSCommodityByCodeEntity` stores HS code descriptions plus mapped Maersk commodity metadata.

## Runtime shape

- GET flows read from startup-loaded maps.
- POST `/commodities` intersects in-memory commodity results with a remote restriction list.
