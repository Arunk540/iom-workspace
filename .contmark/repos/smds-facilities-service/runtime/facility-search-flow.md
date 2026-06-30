---
category: runtime
title: Facility search flow
summary: `/facilities/search` validates facility type plus search filters, enforces customer-code rules, and either runs dynamic JSONB criteria or specialized repository queries.
primary_for: [facilities-parameter-search]
mentions: [/facilities/search, cityname, facilitycategory, customercode, dbconnector]
scenarios:
  - trace search flow
  - debug geography filters
  - inspect customer search rules
  - inspect category search
  - inspect dynamic criteria query
capabilities: [request-flow]
domains: [smds-facilities]
entities: [FacilitySearch, FacilityDomainModel, DbConnector, FacilitiesReactiveRepository]
sources:
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/controller/SmdsFacilitiesController.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/service/FacilitiesSearchService.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/validator/RequestValidator.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/domain/service/FacilitySearchDomainService.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/integrator/FacilityIntegrator.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/connector/DbConnector.java
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - contracts/api-contracts.md
  - domain/customer-access.md
---

# Facility search flow

1. `GET /facilities/search` accepts optional `facilityName`, address/location filters, `facilityCategory`, `facilityId`, and optional `customerCode`, but requires `facilityType` before creating `FacilitySearch`. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/controller/SmdsFacilitiesController.java:176-284)
2. `FacilitiesSearchService.searchFacility(...)` validates the request, enforces a minimum facility-name length when present, maps to `FacilityDomainModel`, delegates to `FacilitySearchDomainService`, converts empty results into `DataNotFoundException`, and maps to `FacilitiesResponse`. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/service/FacilitiesSearchService.java:39-58)
3. `RequestValidator.validateSearchParams(...)` requires `facilityType`, requires `customerCode` for CUST, caps customer-code count, blocks ForgeRock customer mismatches, and demands at least one non-customer filter for non-CUST searches. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/validator/RequestValidator.java:119-167)
4. `FacilityIntegrator.findByQueryParameter(...)` branches to `findByFacilityIdAndCustomerCodes`, customer-only search, OPS `facilityCategory` search, or the generic `DbConnector.fetchFacilityData(...)` path depending on the supplied filters. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/integrator/FacilityIntegrator.java:241-317)
5. The generic DB path always constrains `facility_type` and `status_code = 'A'`, then adds case-insensitive criteria for city, country, region, street, building number, prefix-style facility name, postal code, and facility category. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/connector/DbConnector.java:34-97)
6. Specialized repository queries cover customer-code-only CUST search, facility-id plus customer-code search, and OPS category lookup using JSONB array predicates against `customerCodes` and `facilityCategories`. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/repository/FacilitiesReactiveRepository.java:42-89)

## Related

- [[domain/customer-access]]
- [[contracts/db-schemas]]
