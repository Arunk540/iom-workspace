---
category: navigation
title: REST entry points
summary: SmdsFacilitiesController exposes three reactive GET endpoints for id lookup, multi-parameter search, and fuzzy name search.
primary_for: [facilities-rest-entrypoints]
mentions: [smdsfacilitiescontroller, /facilities, /facilities/search, /facilities/fuzzy-search, webflux]
scenarios:
  - find facility endpoint
  - inspect search routes
  - map rest handlers
  - check api paths
  - trace controller entrypoints
capabilities: [route-discovery]
domains: [smds-facilities]
entities: [FacilityInput, FacilitySearch, FacilitiesResponse]
sources:
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/controller/SmdsFacilitiesController.java
  - componenttest/src/test/resources/features/GETFacility.feature
  - componenttest/src/test/resources/features/GETFacilitySearch.feature
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - navigation/scenarios.md
  - contracts/api-contracts.md
---

# REST entry points

| Method | Path | Start here | Notes |
|---|---|---|---|
| GET | `/facilities` | `SmdsFacilitiesController.getFacilities` | Required `id`; optional `idType`, `facilityType`, `customerCode`, `sortParam`. |
| GET | `/facilities/search` | `SmdsFacilitiesController.getFacilitiesSearch` | Required `facilityType`; optional geography, customerCode, facilityCategory, facilityId. |
| GET | `/facilities/fuzzy-search` | `SmdsFacilitiesController.getFacilitiesFuzzySearch` | Required `facilityName` and `facilityType`; optional `customerCode`, `facilityCategory`. |

## Coverage hints

- `GETFacility.feature` exercises `/facilities` scenarios for default `siteGeoId`, explicit `idType`, `facilityType`, and customer-code filtering.
- `GETFacilitySearch.feature` exercises `/facilities/search` for OPS, COMM, and CUST searches, plus 400/401/403/404 paths.
- No separate component feature was found for `/facilities/fuzzy-search`; the contract is defined in controller annotations.

## Related

- [[navigation/scenarios]]
- [[contracts/api-contracts]]
