---
category: integrations
title: Temporal
summary: Temporal is used only for repricing. This service both connects to a Temporal server and hosts the worker for `REPRICING_TASK_QUEUE`.
primary_for: [temporal-workflow-execution]
mentions: [repricing-workflow-execution, temporal-tls-config, repricing-task-queue]
scenarios:
  - temporal connection issue
  - repricing worker issue
  - workflow client config
  - temporal tls problem
  - repricing task queue
capabilities: [repricing]
domains: [IOM]
entities: [WorkflowServiceStubs, WorkflowClient, RepricingWorkflow]
peer_systems: [temporal]
direction: bidirectional
protocol: temporal-signal
topic_or_endpoint: REPRICING_TASK_QUEUE
sources:
  - src/main/kotlin/com/maersk/iom/config/TemporalConfig.kt
  - src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt
  - src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt
  - src/main/resources/application.yaml
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - runtime/repricing-flow.md
  - operations/retries.md
---

# Temporal integration

- `TemporalConfig` only loads when `temporal.enabled=true`, then builds `WorkflowServiceStubsOptions` against `spring.temporal.connection.target` (source: src/main/kotlin/com/maersk/iom/config/TemporalConfig.kt:41; source: src/main/resources/application.yaml:96)
- TLS can be enabled through `temporal.tls.enabled`; certificate and key are sourced from base64 properties and converted to PKCS#8 before creating the SSL context (source: src/main/kotlin/com/maersk/iom/config/TemporalConfig.kt:54; source: src/main/resources/application.yaml:70)
- `workflowClient()` creates a shared `WorkflowClient` bean for scheduler-side workflow starts (source: src/main/kotlin/com/maersk/iom/config/TemporalConfig.kt:63)
- `RepricingScheduler` starts `RepricingWorkflow` executions on task queue `REPRICING_TASK_QUEUE` with workflow ids prefixed `REPRICING_REQUIRED-` (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt:58)
- `spring.temporal.workers[0]` binds the same queue to `com.maersk.iom.workflows.repricing.RepricingWorkflowImpl` and activity bean `repricingActivityImpl` (source: src/main/resources/application.yaml:99)
- The workflow implementation has one activity stub, `RepricingActivity.repriceBooking(...)`, so Temporal orchestration ends at one outbound HTTP call (source: src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt:21)
