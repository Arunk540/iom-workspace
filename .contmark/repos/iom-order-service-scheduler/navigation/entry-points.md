---
category: navigation
title: Entry points
summary: Scheduler-driven service with one Temporal worker registration and no traced REST or Kafka consumer entry points. Runtime starts from cron and fixed-delay jobs plus the REPRICING_TASK_QUEUE worker.
primary_for: [scheduler-entry-points]
mentions: [temporal-worker-registration, kafka-producer-publishing, archival-jobs]
scenarios:
  - scheduler not running
  - where jobs start
  - which class runs repricing
  - archive job flow
  - temporal worker entry
capabilities: [outbox-publishing, repricing, archival, offer-cleanup, transform]
domains: [IOM]
entities: [OutboxMessage, ServicePlanEntity, AuditEntity]
sources:
  - src/main/kotlin/com/maersk/iom/outbox/scheduler/MessagePublishingScheduler.kt
  - src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt
  - src/main/kotlin/com/maersk/iom/archive/OrderArchivalScheduler.kt
  - src/main/kotlin/com/maersk/iom/archive/AuditArchivalScheduler.kt
  - src/main/kotlin/com/maersk/iom/offer/OfferScheduler.kt
  - src/main/kotlin/com/maersk/iom/transform/TransformScheduler.kt
  - src/main/resources/application.yaml
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - runtime/repricing-flow.md
  - contracts/kafka-events.md
---

# Entry points

| Type | Trigger | Start here | Notes |
|---|---|---|---|
| Scheduled job | `outbox.scheduler.fixedDelay` + `initialDelay` | `MessagePublishingScheduler.checkAndPublishMessage` | Polls DB outbox and routes supported event types through `MessageProducerFactory`. |
| Scheduled job | `repricing.scheduler.delay-ms` | `RepricingScheduler.scheduleRepricing` | Polls repricing-required outbox records and starts Temporal workflows when `repricing.scheduler.active=true`. |
| Scheduled job | `archive.scheduler.orders.cron` | `OrderArchivalScheduler.runScheduledTask` | Archives aged `ServicePlanEntity` rows. |
| Scheduled job | `archive.scheduler.audits.cron` | `AuditArchivalScheduler.runScheduledTask` | Archives aged `AuditEntity` rows. |
| Scheduled job | `offers.cleanup.scheduler.cron` | `OfferScheduler.runOfferCleanupJob` | Deletes old offered-service-plan records over HTTP. |
| Scheduled job | `transform.scheduler.cron` | `TransformScheduler.runScheduledTask` | Calls `OrderRepository.fetchAndTransformNonLatest`. |
| Temporal worker | `REPRICING_TASK_QUEUE` | `RepricingWorkflowImpl.reprice` → `RepricingActivityImpl.repriceBooking` | Worker classes come from `spring.temporal.workers[0]`. |

## Absent entry-point families

- No `@KafkaListener` handlers were found in `src/main/kotlin` during the trace.
- No `@RestController` or MVC/WebFlux controller classes were found in `src/main/kotlin` during the trace.
- Inbound work therefore enters through schedulers or through the Temporal worker defined in configuration.
