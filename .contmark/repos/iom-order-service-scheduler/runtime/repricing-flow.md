---
category: runtime
title: Repricing flow
summary: Repricing starts from outbox polling, launches a Temporal workflow, then calls Web Integrator over HTTP from a Temporal activity. The workflow has one variant: optional `priceCalculationDate` propagation.
primary_for: [repricing-workflow-execution]
mentions: [event-activity-matrix, temporal-worker-registration, web-integrator-repricing]
scenarios:
  - repricing not working
  - why repricing failing
  - where repricing starts
  - trigger repricing workflow
  - repricing flow
capabilities: [repricing]
domains: [IOM]
entities: [OutboxMessage, RepricingRequired, RepriceRequest, ServicePlan]
sources:
  - src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt
  - src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt
  - src/main/kotlin/com/maersk/iom/offer/WebIntegratorClient.kt
  - src/main/resources/application.yaml
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - runtime/event-activity-matrix.md
  - integrations/iom-web-integrator.md
---

# Repricing flow

## Variant Routing

| Trigger condition | Resolver class | Notes |
|---|---|---|
| Any outbox record returned by `fetchRepricingRequiredEvents(...)` | `RepricingScheduler.startRepricingWorkflow` | Always starts `RepricingWorkflow` on `REPRICING_TASK_QUEUE`; only `priceCalculationDate` forwarding varies (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:65). |
| `isVesselEtdBasedPcd(...) == true` | `RepricingScheduler.startRepricingWorkflow` | Passes `servicePlan.booking?.priceCalculationDate` into workflow input (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:74) |
| `isVesselEtdBasedPcd(...) == false` | `RepricingScheduler.startRepricingWorkflow` | Starts workflow with `priceCalculationDate = null` (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:77) |

## Call chain

1. `scheduleRepricing()` runs on `repricing.scheduler.delay-ms` when `repricing.scheduler.active=true` (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:23; source: src/main/resources/application.yaml:311)
2. Repository fetch returns candidate outbox rows bounded by batch size, retry count, and processing-time window (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:49)
3. `startRepricingWorkflow()` builds workflow id `REPRICING_REQUIRED-${outboxMessage.key}-${outboxMessage.id}` (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:58)
4. Scheduler deserializes `RepricingRequired` and nested `ServicePlan` from the outbox payload JSON (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:59)
5. Scheduler creates a `RepricingWorkflow` stub on task queue `REPRICING_TASK_QUEUE` (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:65)
6. `WorkflowClient.start(workflow::reprice, servicePlan.servicePlanNumber!!, priceCalculationDate)` starts the Temporal execution asynchronously (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:78)
7. Worker registration in `application.yaml` binds `RepricingWorkflowImpl` and `repricingActivityImpl` to the same queue (source: src/main/resources/application.yaml:100)
8. `RepricingWorkflowImpl.reprice()` calls the only activity stub method, `repriceBooking(...)` (source: src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt:33)
9. `RepricingActivityImpl.repriceBooking()` delegates to `WebIntegratorClientImpl.repriceBooking()` and blocks for at most 2 minutes (source: src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt:50)
10. `WebIntegratorClientImpl` issues `PATCH /service-plans-queries/service-plans/{servicePlanNumber}/reprice` with `Reason.OTHER` and a comment derived from the presence of `priceCalculationDate` (source: src/main/kotlin/com/maersk/iom/offer/WebIntegratorClient.kt:37)

## Async and retry boundaries

- Scheduler-to-Temporal is async; the scheduler saves updated processing time after start and does not wait for workflow completion (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:52)
- Temporal activity retries at most twice and has a 5-minute start-to-close timeout (source: src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt:24)
- HTTP 4xx and 5xx bodies are converted into `RuntimeException`, so failures surface back through the activity/workflow path (source: src/main/kotlin/com/maersk/iom/offer/WebIntegratorClient.kt:44)
