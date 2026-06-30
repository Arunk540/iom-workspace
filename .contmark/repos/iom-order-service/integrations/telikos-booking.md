---
category: integrations
title: Telikos booking and TMS integration
summary: The visible booking-side outbound integration is the Telikos client used for booking config lookup, TMS handoff, execution handoff, and booking confirmation. Kafka customer-history ingestion also defaults to a Telikos-branded service-plan topic in local config.
primary_for: [telikos-booking-integration]
mentions: [telikos, tms, send-to-execution, confirm-booking, config-lookup]
scenarios:
  - inspect telikos call
  - inspect send to tms
  - inspect send to execution
  - inspect booking confirm
  - inspect telikos topic
capabilities: [integrations]
domains: [booking]
entities: [TelikosClient, SendToTmsRequest, SendToExecutionRequest, ConfirmBookingRequest]
sources:
  - src/main/resources/application.yaml
  - src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt
  - src/main/kotlin/com/maersk/iom/messaging/BookingUpdatesEventHandler.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - runtime/booking-event-processing-flow.md
  - runtime/order-creation-flow.md
  - operations/failure-model.md
---
- Runtime config exposes `services.telikos.base-url=${maersk.telikos-base-url}bookings/` and OAuth client credentials for `telikosAuthProvider`. (source: src/main/resources/application.yaml:141-143, src/main/resources/application.yaml:189-206)
- `OrderServiceImpl.updateWithTelikosConfig` reads booking config and copies `isAutomatedExecution` into the aggregate. (source: src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:227-230)
- `OrderServiceImpl.executeBooking` chooses `sendToTms(...)` when automated execution is enabled; otherwise it builds `SendToExecutionRequest` rows per booking equipment and calls `sendToExecution(...)`. (source: src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:779-814)
- `OrderServiceImpl.confirmBooking` calls Telikos with instruction parties/text for booking confirmation. (source: src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:1056-1074)
- `BookingUpdatesEventHandler.executeOrConfirmBooking` is the ingress path that decides when execution or confirmation calls happen after Kafka booking updates. (source: src/main/kotlin/com/maersk/iom/messaging/BookingUpdatesEventHandler.kt:101-138)
- Local YAML also binds `kafka.customer-booking-history-event.topic` to `KAFKA_SERVICE_PLAN_TOPIC`, which defaults to a Telikos-branded service-plan topic name. (source: src/main/resources/application.yaml:381-382, src/main/resources/application.yaml:448-450)
