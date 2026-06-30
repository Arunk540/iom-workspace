---category: navigation
title: Scenario routing
summary: Use this map to jump from common task phrases to the class and method that owns the behavior.
primary_for: [facilities-task-routing]
mentions: [start-here, class.method, controller, service, integrator, validator]
scenarios:
  - trace id lookup
  - debug search filters
  - follow fuzzy search
  - inspect customer authorization
  - find database query owner
  - find request routing
  - trace task routing
capabilities: [task-routing]
domains: [smds-facilities]
entities: [SmdsFacilitiesController, FacilitiesService, FacilitiesSearchService, FacilityIntegrator]
sources:
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/controller/SmdsFacilitiesController.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/service/FacilitiesService.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/service/FacilitiesSearchService.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/integrator/FacilityIntegrator.java
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - navigation/key-classes.md
  - runtime/facility-search-flow.md
---

# Scenario routing

| Task phrase | Start here | Follow next |
|---|---|---|
| look up facility by id | `SmdsFacilitiesController.getFacilities()` | `FacilitiesService.validateAndGetFacilityResponse()` |
| validate idType or facilityType | `RequestValidator.validateParams()` | `FacilityDomainServiceImpl.validateFacilityOptionsAndIdTypeForCust()` |
| debug customer-code access | `FacilitiesService.validateCustomerAndFacilityType()` | `FacilityDomainServiceImpl.validateCustomer()` |
| trace parameter search | `SmdsFacilitiesController.getFacilitiesSearch()` | `FacilitiesSearchService.searchFacility()` |
| trace fuzzy name search | `SmdsFacilitiesController.getFacilitiesFuzzySearch()` | `FacilitiesSearchService.fuzzySearchFacility()` |
| inspect dynamic DB criteria | `FacilityIntegrator.findByQueryParameter()` | `DbConnector.fetchFacilityData()` |
| inspect id-type repository routing | `FacilityIntegrator.findByFacilityId()` | `FacilitiesReactiveRepository.*` finders |
| inspect facility-category search | `FacilityIntegrator.findFacilityDataByFacilityCategory()` | `FacilitiesReactiveRepository.findFacilityDataByFacilityCategory()` |
| inspect response shaping | `FacilityModelResponseMapper.mapToFacilityDomainModel(...)` | `FacilityResponseMapper.convertFacilityResponse()` |

## Related

- [[navigation/key-classes]]
- [[runtime/facility-id-lookup-flow]]
