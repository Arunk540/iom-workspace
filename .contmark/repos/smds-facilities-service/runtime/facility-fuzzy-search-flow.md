---
category: runtime
title: Facility fuzzy search flow
summary: `/facilities/fuzzy-search` is a narrower search path that requires a minimum facility-name string and delegates to a PostgreSQL fuzzy-match query.
primary_for: [facilities-fuzzy-search]
mentions: [/facilities/fuzzy-search, facilityname, similarity, trigram, fuzzy]
scenarios:
  - trace fuzzy search
  - inspect fuzzy validation
  - inspect name similarity query
  - debug fuzzy search results
  - follow fuzzy endpoint flow
capabilities: [request-flow]
domains: [smds-facilities]
entities: [FacilitySearch, FacilitiesReactiveRepository, FacilitiesResponse]
sources:
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/controller/SmdsFacilitiesController.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/service/FacilitiesSearchService.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/validator/RequestValidator.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/domain/service/FacilitySearchDomainService.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/integrator/FacilityIntegrator.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/repository/FacilitiesReactiveRepository.java
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - runtime/facility-search-flow.md
  - contracts/api-contracts.md
---

# Facility fuzzy search flow

1. `GET /facilities/fuzzy-search` requires `facilityName` and `facilityType`, accepts optional `customerCode` and `facilityCategory`, and maps the request through `FacilityParameterMapper.createFacilitySearchInput(...)`. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/controller/SmdsFacilitiesController.java:286-372)
2. `FacilitiesSearchService.fuzzySearchFacility(...)` first enforces the fuzzy-string validation, then the common search validation, and finally routes to `FacilitySearchDomainService.findFacilityByFacilityNameFuzzy(...)`. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/service/FacilitiesSearchService.java:60-79)
3. `RequestValidator.validateFacilityNameFuzzyString(...)` rejects missing names and requires a minimum facility-name length of three characters. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/validator/RequestValidator.java:169-192)
4. The domain layer delegates directly to `FacilityIntegrator.findFacilityDataByFacilityNameFuzzy(...)`, which maps rows and drops null mapped responses. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/domain/service/FacilitySearchDomainService.java:34-39)
5. The repository fuzzy query searches `facility.facility_data_v3` by facility type, requires `status_code = 'A'`, and applies PostgreSQL `% any(string_to_array(...))` matching over tokenized uppercase facility-name words with a `limit 6`. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/repository/FacilitiesReactiveRepository.java:55-63)

## Related

- [[runtime/facility-search-flow]]
- [[contracts/api-contracts]]
