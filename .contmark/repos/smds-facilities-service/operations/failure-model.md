---category: operations
title: Failure model
summary: Most failures are deliberate validation or authorization outcomes, with 500 reserved for uncaught exceptions after the controller/service pipeline.
primary_for: [facilities-failure-model]
mentions: [badrequest, unauthorized, forbidden, notfound, facilityapierror]
scenarios:
  - inspect failure modes
  - inspect validation errors
  - inspect forbidden paths
  - inspect not found behavior
  - inspect error responses
  - understand the failure model
capabilities: [operational-analysis]
domains: [smds-facilities]
entities: [BadRequestException, DataNotFoundException, ForbiddenException, FacilityApiError]
sources:
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/common/exceptions/GlobalExceptionHandler.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/validator/RequestValidator.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/service/FacilitiesSearchService.java
  - componenttest/src/test/resources/features/GETFacility.feature
  - componenttest/src/test/resources/features/GETFacilitySearch.feature
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - contracts/api-contracts.md
  - operations/monitoring.md
---

# Failure model

- `GlobalExceptionHandler` turns `BadRequestException` into 400, `DataNotFoundException` into 404, `ForbiddenException` or `AccessDeniedException` into 403, and all other uncaught exceptions into 500 JSON responses. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/common/exceptions/GlobalExceptionHandler.java:34-94)
- Validation failures are raised before any repository call for missing/invalid `id`, invalid `idType`, invalid `facilityType`, missing CUST customer codes, oversize customer-code lists, and missing non-CUST search basics. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/validator/RequestValidator.java:72-167)
- Search and fuzzy flows translate an empty result stream into `DataNotFoundException`, making 404 the normal no-data outcome after a valid request. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/service/FacilitiesSearchService.java:39-79)
- Component tests assert 400 and 404 outcomes for `/facilities` on invalid `idType`, invalid `facilityType`, and unmatched IDs. (source: componenttest/src/test/resources/features/GETFacility.feature:129-225)
- Component tests assert 401 for invalid or missing auth, plus 403 for disallowed CUST multi-customer access on `/facilities/search`. (source: componenttest/src/test/resources/features/GETFacilitySearch.feature:136-212)

## Related

- [[contracts/api-contracts]]
- [[operations/monitoring]]
