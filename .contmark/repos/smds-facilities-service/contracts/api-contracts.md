---category: contracts
title: API contracts
summary: The REST contract is generated from controller and OpenAPI annotations rather than a checked-in OpenAPI file.
primary_for: [facilities-api-contracts]
mentions: [openapi, authorization, idtype, facilitytype, facilitiesresponse]
scenarios:
  - inspect api contract
  - inspect endpoint parameters
  - inspect response codes
  - inspect auth requirement
  - inspect generated openapi
  - find the api contracts
  - check contracts definitions
capabilities: [contract-analysis]
domains: [smds-facilities]
entities: [SmdsFacilitiesController, FacilitiesResponse, Error]
sources:
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/SmdsFacilitiesApplication.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/controller/SmdsFacilitiesController.java
  - componenttest/src/test/resources/features/GETFacility.feature
  - componenttest/src/test/resources/features/GETFacilitySearch.feature
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - navigation/entry-points.md
  - operations/failure-model.md
---

# API contracts

- The service publishes OpenAPI metadata with title `APIs`, version `1.0`, and a bearer `Authorization` security scheme from `SmdsFacilitiesApplication`. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/SmdsFacilitiesApplication.java:11-22)
- `GET /facilities` returns `Flux<FacilitiesResponse>` and documents 200/400/401/403/404/500 responses; it requires `id` and supports `idType` values `siteGeoId|siteRkst|cityGeoId|cityRkst|facilityCode`, `facilityType` values `CUST|OPS|COMM`, optional comma-separated `customerCode`, and `sortParam` values `facilityName|facilityType`. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/controller/SmdsFacilitiesController.java:58-158)
- `GET /facilities/search` also returns `Flux<FacilitiesResponse>` and documents the same response codes; it requires `facilityType` and accepts facility-name, address, country/region/city, `postalCode`, optional `facilityCategory`, optional `facilityId`, and optional `customerCode`. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/controller/SmdsFacilitiesController.java:176-284)
- `GET /facilities/fuzzy-search` requires `facilityName` plus `facilityType`, preserves the same response-code set, and narrows optional inputs to `customerCode` and `facilityCategory`. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/controller/SmdsFacilitiesController.java:286-372)
- Component tests cover `/facilities` happy-path and validation scenarios for default `siteGeoId`, explicit `idType`, `facilityType`, and customer-code lookups. (source: componenttest/src/test/resources/features/GETFacility.feature:7-107)
- Component tests cover `/facilities/search` success, not-found, bad-request, unauthorized, and forbidden cases across OPS/COMM/CUST searches. (source: componenttest/src/test/resources/features/GETFacilitySearch.feature:7-212)

## Related

- [[navigation/entry-points]]
- [[operations/failure-model]]
