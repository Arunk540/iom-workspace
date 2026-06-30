---
category: operations
title: Retries and circuit breaking
summary: All outbound retry behavior is centralized, and billing cost updates add a dedicated circuit breaker on top of the shared retry policy.
primary_for: [retry-behavior]
mentions: [retries, backoff, circuit breaker, transient errors]
scenarios:
  - retry policy
  - backoff settings
  - circuit breaker rules
  - transient error handling
  - retry debug
capabilities: [operations-retries]
domains: [Operations]
entities: [RetryConfiguration, BillingClient]
sources:
  - src/main/resources/application.yml
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/ErrorHandler.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/BillingClient.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - architecture/cross-cutting.md
  - operations/failure-model.md
---
# Retries and circuit breaking

- Global retry settings come from `services.external.retry`: count `3`, minimum backoff `3` seconds, jitter `0.75`. (source: src/main/resources/application.yml:89)
- `RetryConfiguration` binds those three properties for every client that opts into retries. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/ErrorHandler.kt:17)
- `configureRetry(...)` retries only `WebClientResponseException` cases where the status is 5xx or `409 CONFLICT`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/ErrorHandler.kt:56)
- Retry exhaustion logs the final failure and rethrows the last exception. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/ErrorHandler.kt:75)
- Billing cost updates add `@CircuitBreaker(name = "billingClientInstance", fallbackMethod = "fallback")` on top of retries. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/BillingClient.kt:60)
- Resilience4j config wires `billingClientInstance` to the shared default circuit-breaker profile. (source: src/main/resources/application.yml:250)
