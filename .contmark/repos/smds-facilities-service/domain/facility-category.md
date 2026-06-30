---
category: domain
title: Facility category
summary: Facility categories are modeled as code/name pairs and are searchable for OPS records in the repository layer.
primary_for: [smds-facility-category]
mentions: [facilitycategory, facilitycategories, code, name, ops]
scenarios:
  - inspect facility categories
  - trace category filter
  - find category code field
  - inspect ops category lookup
  - inspect category payload
capabilities: [domain-modeling]
domains: [smds-facilities]
entities: [FacilityCategory, FacilitiesDomainResponse]
sources:
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/domain/model/common/FacilityCategory.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/domain/model/smds/FacilitiesDomainResponse.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/repository/FacilitiesReactiveRepository.java
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - runtime/facility-search-flow.md
  - contracts/db-schemas.md
---

# Facility category

## Model

- `FacilityCategory` is a simple `{code, name}` pair.
- `FacilitiesDomainResponse` returns `facilityCategories` as a list attached to each facility.

## Search usage

- Category-aware querying is currently hard-coded for OPS data in `FacilitiesReactiveRepository.findFacilityDataByFacilityCategory(...)` and selected from `FacilityIntegrator.findByQueryParameter(...)` when `facilityCategory` is present.

## Related

- [[runtime/facility-search-flow]]
- [[contracts/db-schemas]]
