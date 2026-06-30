---
category: operations
title: Failure model
summary: Most failures originate in outbound dependencies or DB polling. Schedulers either stop the tick, continue per-record, or mark outbox rows failed depending on the feature path.
primary_for: [failure-points]
mentions: [outbox-failures, temporal-failures, archival-failures]
scenarios:
  - where failures happen
  - why scheduler stuck
  - archive failure path
  - kafka publish failure
  - repricing failure model
capabilities: [operations]
domains: [IOM]
entities: [OutboxMessage, ArchivalMetaEntity, RuntimeException]
sources:
  - src/main/kotlin/com/maersk/iom/outbox/scheduler/MessagePublishingScheduler.kt
  - src/main/kotlin/com/maersk/iom/outbox/scheduler/MessageProducerFactory.kt
  - src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt
  - src/main/kotlin/com/maersk/iom/messaging/ServicePlanEventsProducer.kt
  - src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventsProducer.kt
  - src/main/kotlin/com/maersk/iom/messaging/BookingStatusChangedProducer.kt
  - src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt
  - src/main/kotlin/com/maersk/iom/offer/WebIntegratorClient.kt
  - src/main/kotlin/com/maersk/iom/offer/OfferClient.kt
  - src/main/kotlin/com/maersk/iom/archive/service/BlobUploader.kt
  - src/main/kotlin/com/maersk/iom/archive/service/OrderArchivalService.kt
  - src/main/kotlin/com/maersk/iom/archive/service/AuditArchivalService.kt
  - src/main/kotlin/com/maersk/iom/transform/TransformScheduler.kt
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - operations/retries.md
  - operations/monitoring.md
---

# Failure points

| Area | Failure behavior |
|---|---|
| Outbox scheduler routing | Unsupported `OutboxMessage.type` causes `Mono.error(...)`, which fails that scheduler chain unless handled upstream (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/MessageProducerFactory.kt:27) |
| Kafka publish | Producer failures update outbox status and can leave the row `PENDING` for retry or `FAILED` after ceiling/non-retriable error (source: src/main/kotlin/com/maersk/iom/messaging/ServicePlanEventsProducer.kt:63; source: src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventsProducer.kt:108; source: src/main/kotlin/com/maersk/iom/messaging/BookingStatusChangedProducer.kt:70) |
| Repricing workflow | Workflow start is async; later activity or HTTP failures happen after the scheduler already saved processing time, so troubleshooting must inspect Temporal execution history rather than only scheduler logs (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:52; source: src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt:33) |
| Web Integrator / Offer Service | HTTP 4xx/5xx responses are converted to `RuntimeException`; repricing failures surface through Temporal activity, offer cleanup failures fail the scheduler tick when `block()` returns error (source: src/main/kotlin/com/maersk/iom/offer/WebIntegratorClient.kt:44; source: src/main/kotlin/com/maersk/iom/offer/OfferClient.kt:27) |
| Order archival | Batch continues on per-record errors because `onErrorContinue` logs and skips failed entities; successful rows still save metadata then delete source rows (source: src/main/kotlin/com/maersk/iom/archive/service/OrderArchivalService.kt:43) |
| Audit archival | Same continue-on-error pattern applies; metadata save returns a key suffix that is passed into `deleteById(...)` after upload (source: src/main/kotlin/com/maersk/iom/archive/service/AuditArchivalService.kt:47) |
| Azure upload | `IllegalStateException` from SDK upload is logged and ignored before `ArchivalMetaEntity` is returned to the archival service (source: src/main/kotlin/com/maersk/iom/archive/service/BlobUploader.kt:25) |
| Transform scheduler | No explicit error recovery is attached around `fetchAndTransformNonLatest(...).blockLast()`, so repository failures abort that invocation (source: src/main/kotlin/com/maersk/iom/transform/TransformScheduler.kt:32) |
