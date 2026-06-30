---
category: domain
title: Facility aggregate
summary: `FacilityDomainModel` is the cross-layer carrier for facility lookups, while `FacilitiesDomainResponse` defines the business payload returned to clients.
primary_for: [smds-facility-entity]
mentions: [facilitydomainmodel, facilitiesdomainresponse, facilitytype, idtype, customercodes]
scenarios:
  - inspect facility entity
  - find response fields
  - trace domain carrier
  - inspect facility payload
  - inspect facility search model
capabilities: [domain-modeling]
domains: [smds-facilities]
entities: [FacilityDomainModel, FacilitiesDomainResponse, FacilitiesResponse]
sources:
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/domain/model/FacilityDomainModel.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/domain/model/smds/FacilitiesDomainResponse.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/model/FacilitiesResponse.java
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - domain/geographic-details.md
  - domain/customer-access.md
---

# Facility aggregate

## Core carrier

- `FacilityDomainModel` carries inbound identifiers (`idSet`, `idType`, `facilityType`), search filters, customer-code context, and the eventual mapped response object.
- `FacilitiesDomainResponse` contains `geographicDetails`, classification and lifecycle codes, opening times, validity period, facility codes, customer codes, and facility categories.
- `FacilitiesResponse` mirrors the domain response as the outward API contract.

## Practical reading guide

- If you are changing inbound request semantics, start with `FacilityDomainModel` and `FacilityInput`/`FacilitySearch`.
- If you are changing the JSON returned to clients, start with `FacilitiesDomainResponse` and `FacilitiesResponse`.

## Related

- [[domain/geographic-details]]
- [[contracts/api-contracts]]
