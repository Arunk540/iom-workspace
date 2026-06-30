---
category: operations
title: Failure model
summary: The service distinguishes validation failures, forbidden access, partial content, partial updates, external dependency errors, and health-related service unavailability.
primary_for: [failure-model]
mentions: [errors, partial content, validation, external api]
scenarios:
  - failure model
  - error mapping
  - partial update behavior
  - validation error rules
  - dependency failure handling
capabilities: [operations-failure-model]
domains: [Operations]
entities: [WebIntegratorExceptionHandler, PartialContentException, PartialUpdateException]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/exception/WebIntegratorExceptionHandler.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/OrderService.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/ErrorHandler.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - operations/retries.md
  - architecture/cross-cutting.md
---
# Failure model

- Validation failures such as bad request bodies or business field checks become `400` JSON responses through `ServerWebInputException` and `FieldValidationException` handlers. (source: src/main/kotlin/com/maersk/iom/webintegrator/exception/WebIntegratorExceptionHandler.kt:50)
- `PartialContentException` and `PartialUpdateException` both map to `206 PARTIAL_CONTENT`, preserving partial payloads so callers can continue with degraded data. (source: src/main/kotlin/com/maersk/iom/webintegrator/exception/WebIntegratorExceptionHandler.kt:131)
- `OrderService.addCosts(...)` converts non-404 billing failures into `PartialContentException` so order reads can still return the base service plan. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OrderService.kt:247)
- Billing-update vendor lookup or billing push failures become `PartialUpdateException`, preserving the already-updated order response. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OrderService.kt:418)
- Central webclient error handling maps 5xx responses to `ExternalApiException` and extended external failures to `ExtendedExternalApiException`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/ErrorHandler.kt:25)
- `ServiceUnavailableException` is explicitly surfaced as HTTP 503 by controller advice. (source: src/main/kotlin/com/maersk/iom/webintegrator/exception/WebIntegratorExceptionHandler.kt:208)
