---
category: runtime
title: Facility id lookup flow
summary: `/facilities` resolves JWT claims, validates identifiers, routes by `idType`, enforces CUST rules, and maps active DB rows back to the API response.
primary_for: [facility-id-lookup]
mentions: [/facilities, idtype, customercode, sortparam, facilityintegrator]
scenarios:
  - trace id lookup flow
  - debug idtype routing
  - inspect customer code filtering
  - inspect response sorting
  - follow facility get path
capabilities: [request-flow]
domains: [smds-facilities]
entities: [FacilityInput, FacilityDomainModel, FacilitiesResponse]
sources:
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/controller/SmdsFacilitiesController.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/service/FacilitiesService.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/validator/RequestValidator.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/domain/service/FacilityDomainServiceImpl.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/integrator/FacilityIntegrator.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/repository/FacilitiesReactiveRepository.java
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - navigation/entry-points.md
  - operations/failure-model.md
---

# Facility id lookup flow

1. `GET /facilities` requires `Authorization` and `id`, accepts optional `idType`, `facilityType`, `customerCode`, and `sortParam`, then maps JWT claims plus query params into `FacilityInput`. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/controller/SmdsFacilitiesController.java:58-158)
2. `FacilitiesService.validateAndGetFacilityResponse(...)` runs `RequestValidator.validateParams`, performs customer/facility-type checks, builds `FacilityDomainModel`, delegates to the domain service, maps to `FacilitiesResponse`, and sorts by `sortParam` with a default of facility name. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/service/FacilitiesService.java:45-119)
3. `RequestValidator.validateParams(...)` rejects missing `id`, invalid `idType`, and invalid `facilityType`, while `validateIds(...)` enforces the comma-separated ID regex and max-count rule. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/validator/RequestValidator.java:46-98)
4. When customer codes are present, `FacilityDomainServiceImpl.validateFacilityOptionsAndIdTypeForCust(...)` only allows `facilityCode`, `cityGeoId`, or `cityRkst` with facility type `CUST`, and `validateCustomer(...)` rejects JWT/customer-code mismatches. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/domain/service/FacilityDomainServiceImpl.java:33-61)
5. `FacilityIntegrator.findByFacilityId(...)` switches on `IdType` and routes to site/city geo/RKST lookups, facility-code lookup, and facility-type-aware variants, always mapping `FacilityData` rows back through `FacilityModelResponseMapper`. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/integrator/FacilityIntegrator.java:38-99)
6. Repository access is anchored on `facility.facility_data_v3`; named finders enforce `status_code = 'A'` for geo/RKST routes, and the customer-code path uses a JSONB `EXISTS` clause over `facility_data->'customerCodes'`. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/repository/FacilitiesReactiveRepository.java:16-18)

## Related

- [[navigation/entry-points]]
- [[operations/failure-model]]
