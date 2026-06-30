---
category: domain
title: Customer access rules
summary: Customer codes are both query inputs and authorization constraints, especially for CUST facilities and ForgeRock tokens.
primary_for: [smds-customer-access]
mentions: [customercode, jwtcustomercode, cust, forgerock, tokenScope]
scenarios:
  - inspect customer authorization
  - trace customer code rules
  - debug cust facility access
  - inspect jwt customer checks
  - inspect multi customer behavior
capabilities: [authorization-modeling]
domains: [smds-facilities]
entities: [CustomerCode, JwtCustomerCode, FacilityInput, FacilitySearch]
sources:
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/model/FacilityInput.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/model/FacilitySearch.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/domain/service/FacilityDomainServiceImpl.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/validator/RequestValidator.java
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - runtime/facility-id-lookup-flow.md
  - runtime/facility-search-flow.md
---

# Customer access rules

## Rules of record

- Query-time customer codes are stored as sets in both `FacilityInput` and `FacilitySearch`.
- For id-based CUST lookups, only `facilityCode`, `cityGeoId`, and `cityRkst` are allowed with customer codes.
- ForgeRock requests are rejected when requested customer codes do not include the JWT customer code, and search requests also enforce token-scope constraints for multi-customer access.

## Related

- [[runtime/facility-id-lookup-flow]]
- [[runtime/facility-search-flow]]
