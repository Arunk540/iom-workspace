---category: operations
title: Failure model
summary: Explains how iom-application surfaces validation, integration, resolver, and persistence failures without hiding them.
primary_for: [library-failure-model]
mentions: [validation failure, retry policy, resolver failure, optimistic lock]
scenarios:
  - how errors surface
  - why retry happens
  - resolver failure behavior
  - find lock version
  - where incidents stored
  - understand the failure model
capabilities: [failure-analysis]
domains: [operations]
entities: [ValidationException, ErrorHandler, OutboxMessage, IncidentEntity]
sources:
  - application-validators/src/main/kotlin/com/maersk/iom/validator/IOMValidator.kt
  - iom-common/src/main/kotlin/com/maersk/iom/common/exception/ValidationException.kt
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/ErrorHandler.kt
  - resolvers/src/main/kotlin/com/maersk/iom/resolvers/Resolver.kt
  - resolvers/src/main/kotlin/com/maersk/iom/resolvers/PersistedServicePlanResolver.kt
  - iom-common/src/main/kotlin/com/maersk/iom/common/exception/OptimisticLockException.kt
  - iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/ServicePlanEntity.kt
  - iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/OutboxMessage.kt
  - iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/IncidentEntity.kt
  - iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/AuditEntity.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - operations/flags-and-lists.md
  - contracts/db-schemas.md
---
## Failure classes

- Validation does not throw from validator implementations; the contract returns `Flux<FieldValidationError>`, and `ValidationException` later deduplicates errors by `(code, fieldName)` when aggregated. (source: application-validators/src/main/kotlin/com/maersk/iom/validator/IOMValidator.kt:9) (source: iom-common/src/main/kotlin/com/maersk/iom/common/exception/ValidationException.kt:3)
- External-client failure handling treats HTTP 5xx responses as `ExternalApiException`, while the retry policy backs off only for `ExternalApiException` instances and raises a final `ExternalApiException` when retries are exhausted. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/ErrorHandler.kt:22)
- Resolver contracts reduce operational lookup issues to `ResolverResult.SUCCESS` or `FAILURE`; `PersistedServicePlanResolver` explicitly returns `FAILURE` when the current service plan cannot be loaded or when the lookup errors. (source: resolvers/src/main/kotlin/com/maersk/iom/resolvers/Resolver.kt:6) (source: resolvers/src/main/kotlin/com/maersk/iom/resolvers/PersistedServicePlanResolver.kt:15)
- Persistence uses `lock_version` on `ServicePlanEntity` for optimistic concurrency, and the shared exception set includes an `OptimisticLockException` type for reporting that conflict. (source: iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/ServicePlanEntity.kt:91) (source: iom-common/src/main/kotlin/com/maersk/iom/common/exception/OptimisticLockException.kt:1)
- Publishing and operational recovery use `OutboxMessage` retry/status fields plus `IncidentEntity` and `AuditEntity` tables to retain failed integration attempts, exception/customer-request incidents, and audited change payloads. (source: iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/OutboxMessage.kt:15) (source: iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/IncidentEntity.kt:23) (source: iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/AuditEntity.kt:11)
