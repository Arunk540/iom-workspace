---category: navigation
title: Key classes
summary: The service is centered on one controller, two application services, two domain services, and a single integrator/repository boundary.
primary_for: [facilities-class-map]
mentions: [application, domain, infrastructure, controller, repository, mapper]
scenarios:
  - find owner class
  - map service layers
  - inspect request validation
  - locate repository queries
  - understand response mapping
  - find a key class
capabilities: [class-navigation]
domains: [smds-facilities]
entities: [SmdsFacilitiesController, RequestValidator, FacilitiesService, FacilitiesSearchService, FacilityIntegrator, FacilitiesReactiveRepository]
sources:
  - service/src/main/AGENTS.md
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/AGENTS.md
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/domain/AGENTS.md
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/AGENTS.md
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - architecture/modules.md
  - navigation/scenarios.md
---

# Key classes

| Concern | Class |
|---|---|
| HTTP entry points | `application.controller.SmdsFacilitiesController` |
| Request-to-application mapping | `application.mapper.FacilityParameterMapper` |
| Input validation | `application.validator.RequestValidator` |
| ID-based lookup orchestration | `application.service.FacilitiesService` |
| Search and fuzzy orchestration | `application.service.FacilitiesSearchService` |
| CUST-specific business validation | `domain.service.FacilityDomainServiceImpl` |
| Search domain handoff | `domain.service.FacilitySearchDomainService` |
| Cross-query DB routing | `infrastructure.integrator.FacilityIntegrator` |
| Dynamic criteria query builder | `infrastructure.connector.DbConnector` |
| Fixed SQL / JSONB access | `infrastructure.repository.FacilitiesReactiveRepository` |
| DB row contract | `infrastructure.contract.database.FacilityData` |
| DB row to domain mapping | `infrastructure.mapper.FacilityModelResponseMapper` |
| Domain to API mapping | `application.mapper.FacilityResponseMapper` |
| Error shaping | `common.exceptions.GlobalExceptionHandler` |

## Related

- [[architecture/modules]]
- [[navigation/scenarios]]
