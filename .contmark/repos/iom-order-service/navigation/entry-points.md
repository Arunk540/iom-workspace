---category: navigation
title: Entry points
summary: iom-order-service starts at WebFlux REST controllers, three Kafka consumers, and a LaunchDarkly-triggered customer-history batch runner. Order mutations always delegate into OrderService/OrderServiceImpl from these ingress points.
primary_for: [entry-point-routing]
mentions: [v2-api, v3-api, kafka-consumers, scheduler, order-service]
scenarios:
  - find api entrypoint
  - trace kafka ingress
  - locate scheduler trigger
  - start from controller
  - find mutation owner
  - list the entry points
capabilities: [navigation]
domains: [order]
entities: [ServicePlan, Booking, EventNotification, Incident]
sources:
  - src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt
  - src/main/kotlin/com/maersk/iom/controller/v3/V3DashboardController.kt
  - src/main/kotlin/com/maersk/iom/controller/v3/V3DownloadController.kt
  - src/main/kotlin/com/maersk/iom/controller/v3/ExecutionChargesController.kt
  - src/main/kotlin/com/maersk/iom/controller/v2/DashboardController.kt
  - src/main/kotlin/com/maersk/iom/controller/v2/ReferenceDataController.kt
  - src/main/kotlin/com/maersk/iom/controller/OrderServiceErrorCodesController.kt
  - src/main/kotlin/com/maersk/iom/incident/controller/IncidentController.kt
  - src/main/kotlin/com/maersk/iom/messaging/BookingConsumer.kt
  - src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventConsumer.kt
  - src/main/kotlin/com/maersk/iom/messaging/CustomerBookingHistoryEventConsumer.kt
  - src/main/kotlin/com/maersk/iom/scheduler/CustomerBookingHistoryScheduler.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - navigation/scenarios.md
  - contracts/api-contracts.md
  - contracts/kafka-events.md
---
## REST
- `V3OrderController` (`/v3`) handles create/update, patch, amend, fetch, status, vessel-date patch, charges patch, container patch, FinOps status, FRN update, customer history, container count, and container associations.
- `V3DashboardController` handles `/v3/service-plans/search`, `/multi-search`, and `/search/download`.
- `V3DownloadController` handles `/v3/service-plans/containers/download`.
- `ExecutionChargesController` handles `PUT /service-plans/{servicePlanNumber}/execution-charges`.
- `IncidentController` handles `POST /v3/service-plans/{servicePlanNumber}/incident`.
- `DashboardController` keeps v2 search and stage-count endpoints.
- `ReferenceDataController` exposes enum/reference endpoints.
- `OrderServiceErrorCodesController` exposes `/error-codes`.

## Kafka
- `BookingConsumer.listenForBookingMessage` receives booking Avro messages and routes by `eventType` through `EventDispatcher`.
- `ServicePlanDomainEventConsumer.listenForServicePlanNotification` receives domain-event notifications and audits `SERVICE_PLAN_CHANGED` payloads.
- `CustomerBookingHistoryEventConsumer.listenForCustomerBookingHistoryEvent` transforms Avro `ServicePlan` payloads into customer booking history aggregates.

## Scheduler
- `CustomerBookingHistoryScheduler` is a component, not a cron job; it starts only when LaunchDarkly flag `mci-iom-is-customer-history-scheduler-enabled` flips from OFF to ON once per instance.
