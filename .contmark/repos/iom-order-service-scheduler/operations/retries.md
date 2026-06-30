---
category: operations
title: Retries
summary: Retry behavior is split across outbox status updates, Kafka producer settings, and Temporal activity retries. HTTP clients rely on timeout/error propagation rather than explicit retry operators in this repo.
primary_for: [retry-behavior]
mentions: [outbox-retry-ceiling, temporal-activity-retries, kafka-producer-retries]
scenarios:
  - retry behavior
  - why message failed
  - temporal retry count
  - kafka retry config
  - repricing retry path
capabilities: [operations]
domains: [IOM]
entities: [OutboxMessage, RetryOptions, ProducerConfig]
sources:
  - src/main/kotlin/com/maersk/iom/messaging/ServicePlanEventsProducer.kt
  - src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventsProducer.kt
  - src/main/kotlin/com/maersk/iom/messaging/BookingStatusChangedProducer.kt
  - src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt
  - src/main/kotlin/com/maersk/iom/config/KafkaProducerConfig.kt
  - src/main/kotlin/com/maersk/iom/offer/WebClientConfiguration.kt
  - src/main/resources/application.yaml
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - operations/failure-model.md
  - integrations/temporal.md
---

# Retry behavior

- Outbox producers read `outbox.scheduler.retryCount` and call `message.updateStatus(retryCount + 1, rootCauseMessage, isRetriable, status)` on errors; status becomes `FAILED` when max retries are reached or the error is non-retriable (source: src/main/kotlin/com/maersk/iom/messaging/ServicePlanEventsProducer.kt:69; source: src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventsProducer.kt:114; source: src/main/kotlin/com/maersk/iom/messaging/BookingStatusChangedProducer.kt:71)
- Default outbox retry ceiling is 3 in `application.yaml` (source: src/main/resources/application.yaml:275)
- Kafka producer config also sets low-level producer retries, retry backoff, and delivery timeout through `ProducerConfig.RETRIES_CONFIG`, `RETRY_BACKOFF_MS_CONFIG`, and `DELIVERY_TIMEOUT_MS_CONFIG` (source: src/main/kotlin/com/maersk/iom/config/KafkaProducerConfig.kt:229)
- `RepricingWorkflowImpl` adds Temporal `RetryOptions` with `maximumAttempts(2)` around the activity stub (source: src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt:25)
- Activity execution must finish within a 5-minute start-to-close timeout, and the HTTP call inside the activity blocks at most 2 minutes (source: src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt:24; source: src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt:52)
- `services.external.retry.count|minBackOff|jitter` is declared in configuration, while repo-owned HTTP clients visibly enforce connection/read/write timeouts through the shared Reactor Netty client (source: src/main/resources/application.yaml:65; source: src/main/kotlin/com/maersk/iom/offer/WebClientConfiguration.kt:79)
