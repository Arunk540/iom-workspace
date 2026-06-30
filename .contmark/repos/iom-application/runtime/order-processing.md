---
category: runtime
title: Order processing flow
summary: Explains the library-level execution path from inbound service-plan DTOs through enrichment, validation, persistence, and event creation.
primary_for: [order-processing-flow]
mentions: [runtime flow, processing path, enrichment validation persistence]
scenarios:
  - trace processing flow
  - follow order path
  - understand library runtime
  - map validation pipeline
  - find persistence sequence
capabilities: [runtime-tracing]
domains: [order-management]
entities: [OrderService, Resolver, ServiceDatesValidator, OrderRepositoryImpl]
sources:
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/OrderService.kt
  - resolvers/src/main/kotlin/com/maersk/iom/resolvers/Resolver.kt
  - resolvers/src/main/kotlin/com/maersk/iom/resolvers/CommodityResolver.kt
  - resolvers/src/main/kotlin/com/maersk/iom/resolvers/PartyResolver.kt
  - resolvers/src/main/kotlin/com/maersk/iom/resolvers/PersistedServicePlanResolver.kt
  - application-validators/src/main/kotlin/com/maersk/iom/validator/IOMValidator.kt
  - application-validators/src/main/kotlin/com/maersk/iom/validator/ServiceDatesValidator.kt
  - iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/adapter/OrderRepositoryImpl.kt
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/event/DomainEventFactory.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - navigation/scenarios.md
  - contracts/db-schemas.md
---
## Flow stages

1. `OrderService` is the inward port for create/update, amend, status update, patch, retrieval, save, execution, confirmation, Telikos config update, FRN updates, container associations, and vessel-date updates, all through `Mono`/`Flux` methods. (source: iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/OrderService.kt:14)
2. Resolvers implement a shared `Resolver<T>` contract that accepts a reactive service-plan stream and a mutable `ResolvedData` bag; `PersistedServicePlanResolver` loads the current stored plan, `PartyResolver` batch-loads party codes from the customer service, and `CommodityResolver` resolves commodity masters with cache fallback. (source: resolvers/src/main/kotlin/com/maersk/iom/resolvers/Resolver.kt:6) (source: resolvers/src/main/kotlin/com/maersk/iom/resolvers/PersistedServicePlanResolver.kt:15) (source: resolvers/src/main/kotlin/com/maersk/iom/resolvers/PartyResolver.kt:21) (source: resolvers/src/main/kotlin/com/maersk/iom/resolvers/CommodityResolver.kt:26)
3. Validators expose `validate(servicePlan, data, requestContextProvider)` as `Flux<FieldValidationError>`; `ServiceDatesValidator` branches between create and amendment logic, skips some checks during execution-side container deletion, and enforces ordering/past-date rules over leg and equipment service dates. (source: application-validators/src/main/kotlin/com/maersk/iom/validator/IOMValidator.kt:9) (source: application-validators/src/main/kotlin/com/maersk/iom/validator/ServiceDatesValidator.kt:27)
4. Persistence runs through `OrderRepositoryImpl.save()`, which maps the domain aggregate into `ServicePlanEntity`, saves PostgreSQL metadata first, maps the saved entity back to domain, and then writes the document representation through `OrderDocumentRepository`. (source: iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/adapter/OrderRepositoryImpl.kt:38)
5. Reads run through `OrderRepositoryImpl.findById()`, which checks the stored entity version, transforms non-latest payloads, persists the transformed entity, and only then maps back to the latest domain shape. (source: iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/adapter/OrderRepositoryImpl.kt:52)
6. Domain events are materialized by `DomainEventFactory.create()` for service-plan change, service-plan-status change, booking-status change, and repricing-required event types using serialized aggregate payloads plus request context. (source: iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/event/DomainEventFactory.kt:7)
