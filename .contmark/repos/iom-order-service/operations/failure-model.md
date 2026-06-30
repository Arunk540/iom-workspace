---
category: operations
title: Failure model
summary: Most failures come from validation, external dependencies, stale events, persistence conflicts, and acknowledgment timing in consumer flows. The service prefers propagating typed exceptions and logging side effects instead of swallowing domain failures, but some consumer paths intentionally acknowledge or coerce errors.
primary_for: [failure-model]
mentions: [validation, optimistic-lock, consumer-ack, external-failure, access-control]
scenarios:
  - inspect failure points
  - inspect stale event handling
  - inspect validation failure
  - inspect persistence conflict
  - inspect external dependency risk
capabilities: [operations]
domains: [runtime]
entities: [ValidationException, NotFoundException, ForbiddenException, OptimisticLockException]
sources:
  - src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt
  - src/main/kotlin/com/maersk/iom/messaging/BookingUpdatesEventHandler.kt
  - src/main/kotlin/com/maersk/iom/messaging/PricingUpdatesEventHandler.kt
  - src/main/kotlin/com/maersk/iom/messaging/CustomerBookingHistoryEventConsumer.kt
  - src/main/kotlin/com/maersk/iom/service/RolodexAuthService.kt
  - src/main/kotlin/com/maersk/iom/service/ExcelService.kt
  - src/main/kotlin/com/maersk/iom/incident/service/IncidentService.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - operations/retries.md
  - operations/monitoring.md
  - integrations/telikos-booking.md
---
- `OrderServiceImpl` raises validation failures for invalid status transitions, mismatched service-plan numbers, empty query filters, unauthorized customer access, and missing entities; optimistic-lock failures are remapped to `OptimisticLockException`. (source: src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:98-138, src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:187-189, src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:404-424, src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:1168-1199, src/main/kotlin/com/maersk/iom/model/Extensions.kt:279-289)
- Booking Kafka updates can drop stale or invalid work-process events by filtering or empty-switching before save; with ack enabled, those paths still acknowledge. (source: src/main/kotlin/com/maersk/iom/messaging/BookingUpdatesEventHandler.kt:51-62, src/main/kotlin/com/maersk/iom/messaging/BookingUpdatesEventHandler.kt:153-217)
- Pricing handler acknowledges `getOrder` failures when booking ack is enabled and blocks the reactive chain at the handler edge. (source: src/main/kotlin/com/maersk/iom/messaging/PricingUpdatesEventHandler.kt:38-52)
- Customer-history ingestion converts transform/save failures into fallback strings and still acknowledges by default, so bad payloads are observable but not retried forever in-handler. (source: src/main/kotlin/com/maersk/iom/messaging/CustomerBookingHistoryEventConsumer.kt:66-79, src/main/kotlin/com/maersk/iom/messaging/CustomerBookingHistoryEventConsumer.kt:88-177)
- Rolodex auth/download fails fast on missing configuration, token extraction failure, empty responses, or WebClient errors. (source: src/main/kotlin/com/maersk/iom/service/RolodexAuthService.kt:26-49, src/main/kotlin/com/maersk/iom/service/ExcelService.kt:20-38)
- Incident creation fails when requester is not BOOKED_BY/PRICE_OWNER, when the plan is not `CONFIRMED`, or when amend/cancel reason codes are unsupported. (source: src/main/kotlin/com/maersk/iom/incident/service/IncidentService.kt:49-149)
