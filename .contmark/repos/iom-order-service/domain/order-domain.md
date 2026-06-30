---category: domain
title: Order domain orchestration
summary: OrderServiceImpl is the single mutation entry point for controller and consumer writes. It combines resolver enrichment, validation, persistence, incident/business-exception side effects, outbox dispatch, and selected external calls.
primary_for: [order-domain-orchestration]
mentions: [order-service, validators, resolvers, incidents, outbox]
scenarios:
  - inspect mutation pipeline
  - inspect amend rules
  - inspect incident side effects
  - inspect customer history writes
  - inspect outbox dispatch
  - understand the order domain
  - find order logic
capabilities: [domain-orchestration]
domains: [order]
entities: [OrderServiceImpl, ServicePlan, IncidentEntity, OutboxMessage, AuditEntity]
sources:
  - src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt
  - src/main/kotlin/com/maersk/iom/service/BusinessExceptionService.kt
  - src/main/kotlin/com/maersk/iom/incident/service/IncidentService.kt
  - src/main/kotlin/com/maersk/iom/model/event/handler/DomainEventHandlerFacade.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - domain/service-plan.md
  - runtime/booking-event-processing-flow.md
  - operations/failure-model.md
---
- `createOrUpdateOrder`, `amendOrder`, `patch`, `updateCharges`, `updateContainers`, `updateFrn`, and `updateOceanVesselDates` all converge on `save()`.
- `ResolverService` is the common enrichment fan-out before validation.
- Basic validators run before most mutations; business-exception validation can persist incident rows and toggle `instantlyConfirmed` behavior.
- Telikos and planning-deadline calls are the visible domain-layer outbound integrations.
- Domain events leave the mutation path through `DomainEventHandlerFacade` and `OutboxStrategy`, not a direct Kafka producer.
- Customer history is stored through `getCustomerBookingsHistory`, `saveCustomerBookingsHistory`, and `updateCustomerHistoryStatusAsProcessed` on the same service.
