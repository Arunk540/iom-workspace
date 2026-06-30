---category: navigation
title: Key classes
summary: The main classes split cleanly into ingress controllers/consumers, domain orchestration, query services, and event/outbox handlers. These classes are the shortest path to most behavior in the repository.
primary_for: [class-locator]
mentions: [controllers, consumers, domain, queries, outbox]
scenarios:
  - find order owner
  - find search owner
  - find kafka handler
  - find security config
  - find outbox logic
  - find a key class
  - locate the right class
capabilities: [navigation]
domains: [order]
entities: [ServicePlan, Booking, AuditEntity, OutboxMessage]
sources:
  - src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt
  - src/main/kotlin/com/maersk/iom/service/SearchService.kt
  - src/main/kotlin/com/maersk/iom/service/QueryService.kt
  - src/main/kotlin/com/maersk/iom/messaging/BookingUpdatesEventHandler.kt
  - src/main/kotlin/com/maersk/iom/config/SecurityConfig.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - architecture/modules.md
  - architecture/cross-cutting.md
  - domain/order-domain.md
---
- order mutation orchestrator → `com.maersk.iom.order.domain.OrderServiceImpl`
- v3 write API → `com.maersk.iom.controller.v3.V3OrderController`
- v3 dashboard API → `com.maersk.iom.controller.v3.V3DashboardController`
- v2 dashboard API → `com.maersk.iom.controller.v2.DashboardController`
- execution charge adapter → `com.maersk.iom.service.ExecutionChargeService`
- query adapter → `com.maersk.iom.service.QueryService`
- search orchestration → `com.maersk.iom.service.SearchService`
- master-data enrichment fan-out → `com.maersk.iom.service.ResolverService`
- booking Kafka ingress → `com.maersk.iom.messaging.BookingConsumer`
- booking status handler → `com.maersk.iom.messaging.BookingUpdatesEventHandler`
- booking pricing handler → `com.maersk.iom.messaging.PricingUpdatesEventHandler`
- domain event audit consumer → `com.maersk.iom.messaging.ServicePlanDomainEventConsumer`
- customer history consumer → `com.maersk.iom.messaging.CustomerBookingHistoryEventConsumer`
- customer history batch runner → `com.maersk.iom.scheduler.CustomerBookingHistoryScheduler`
- audit persistence logic → `com.maersk.iom.service.audit.AuditService`
- outbox dispatcher facade → `com.maersk.iom.model.event.handler.DomainEventHandlerFacade`
- outbox persistence strategy → `com.maersk.iom.model.event.strategy.OutboxStrategy`
- security and JWT decoding → `com.maersk.iom.config.SecurityConfig`
- request tracing filter → `com.maersk.iom.filter.ObservationWebFilter`
