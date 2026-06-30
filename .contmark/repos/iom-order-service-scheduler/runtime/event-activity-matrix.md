---
category: runtime
title: Event activity matrix
summary: Temporal is only used for repricing. An outbox repricing event becomes one workflow execution and one HTTP-backed activity call.
primary_for: [event-activity-matrix]
mentions: [repricing-workflow-execution, temporal-worker-registration, web-integrator-repricing]
scenarios:
  - repricing event flow
  - temporal activity sequence
  - workflow activity map
  - repricing path
  - event to workflow
capabilities: [repricing]
domains: [IOM]
entities: [OutboxMessage, RepricingRequired, ServicePlan]
sources:
  - src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt
  - src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt
  - src/main/kotlin/com/maersk/iom/offer/WebIntegratorClient.kt
  - src/main/resources/application.yaml
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - runtime/repricing-flow.md
  - integrations/temporal.md
---

# Event activity matrix

| Event / trigger | Resolver | Workflow step | Activity / side effect |
|---|---|---|---|
| Repricing-required outbox row fetched by `fetchRepricingRequiredEvents(batchSize, retryCount, processingTimeLimitMinutes)` | `RepricingScheduler.scheduleRepricing()` polls and hands each message to `startRepricingWorkflow()` (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:46) | Creates workflow stub with task queue `REPRICING_TASK_QUEUE` and starts `workflow::reprice` with `servicePlan.servicePlanNumber!!` plus optional `priceCalculationDate` (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:65) | Saves updated processing time after workflow start (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:52) |
| Temporal worker receives task on `REPRICING_TASK_QUEUE` from `spring.temporal.workers[0]` | Worker registration wires `RepricingWorkflowImpl` and bean `repricingActivityImpl` (source: src/main/resources/application.yaml:99) | `RepricingWorkflowImpl.reprice()` invokes `activity.repriceBooking(servicePlanNumber, priceCalculationDate)` (source: src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt:33) | Activity uses `WebIntegratorClient.repriceBooking()` and blocks up to 2 minutes for the HTTP response (source: src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt:50) |
| Feature-gated price-date enrichment | `featureConfigUtil.isVesselEtdBasedPcd(bookingCountryCode, priceOwnerCode)` decides whether `servicePlan.booking?.priceCalculationDate` is forwarded (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:74) | Forwarded `priceCalculationDate` becomes workflow input (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:77) | HTTP PATCH body comment changes based on whether the date is present (source: src/main/kotlin/com/maersk/iom/offer/WebIntegratorClient.kt:29) |

## Activity policy

- Start-to-close timeout is 5 minutes for the Temporal activity stub (source: src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt:24)
- Activity retry `maximumAttempts` is 2 inside the workflow implementation (source: src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt:27)
