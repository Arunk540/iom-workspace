---category: architecture
title: Cross-cutting concerns
summary: Security, reactive execution, OpenAPI metadata, global exception shaping, and metrics/logging are the main cross-cutting mechanisms.
primary_for: [facilities-cross-cutting]
mentions: [oauth2, preauthorize, webflux, openapi, actuator, log4j2]
scenarios:
  - inspect auth model
  - inspect observability
  - inspect error handling
  - inspect reactive runtime
  - inspect api documentation
  - search the facilities
  - find facilities details
capabilities: [cross-cutting-analysis]
domains: [smds-facilities]
entities: [SecurityUtils, GlobalExceptionHandler, SmdsFacilitiesApplication, management.endpoints]
sources:
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/SmdsFacilitiesApplication.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/controller/SmdsFacilitiesController.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/common/exceptions/GlobalExceptionHandler.java
  - service/src/main/resources/application.yml
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - operations/monitoring.md
  - operations/failure-model.md
---

# Cross-cutting concerns

- The application is a Spring Boot WebFlux service and declares an OpenAPI definition plus a bearer `Authorization` security scheme in the main application class.
- Every controller method is guarded with `@PreAuthorize` against the configured authority list and pulls JWT claims through `SecurityUtils.getClaims()`.
- `GlobalExceptionHandler` normalizes 400, 404, 403, and unexpected 500 responses into `FacilityApiError` JSON payloads.
- Actuator exposes `health`, `info`, and `prometheus`, and HTTP request latency percentiles/histograms are enabled.
- Logging is tuned by package, with application and infrastructure packages at INFO and the domain layer at DEBUG in the default profile.

## Related

- [[operations/monitoring]]
- [[operations/failure-model]]
