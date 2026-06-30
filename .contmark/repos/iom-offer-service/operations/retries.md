---category: operations
title: Retry and Circuit Breaker Configuration
summary: Retry policies and Resilience4j circuit breaker configuration for all external calls
primary_for: [retry-circuit-breaker-config-reference, resilience-policy-guide]
mentions: [ErrorHandler, RetryConfiguration, WebClientConfiguration, RatesClientImpl, OfferClientImpl, RoutingClientImpl]
scenarios:
  - find retry count for external calls
  - understand circuit breaker thresholds
  - understand when retries are skipped
  - find circuit breaker instance names
  - understand fallback behaviour on open circuit
  - find resilience policies
  - check retry resilience
capabilities: [offer-search, rate-calculation, routing]
domains: [offer-management, rates, routing]
entities: []
sources:
  - src/main/kotlin/com/maersk/iom/offer/webclient/ErrorHandler.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [operations/failure-model.md, architecture/cross-cutting.md]
---

## Retry Policy

All WebClients share a single `RetryConfiguration` bean (source: src/main/kotlin/com/maersk/iom/offer/webclient/ErrorHandler.kt:RetryConfiguration):

| Parameter | Value | Config key |
|---|---|---|
| Max attempts | 3 | `services.external.retry.count` |
| Min back-off | 2 s | `services.external.retry.minBackOff` |
| Jitter | 0.75 | `services.external.retry.jitter` |

`ErrorHandler.configureRetry()` applies `Retry.backoff(count, minBackOff).jitter(jitter)` (source: src/main/kotlin/com/maersk/iom/offer/webclient/ErrorHandler.kt:configureRetry)

**Retry filter**: only `ExtendedExternalApiException` is retried; 422 Unprocessable Entity is excluded (source: src/main/kotlin/com/maersk/iom/offer/webclient/ErrorHandler.kt:configureRetry)

On retry exhausted: logs error and re-throws `ExtendedExternalApiException` with the original status code.

## Circuit Breaker Configuration

Framework: Resilience4j; `circuitBreakerAspectOrder: 1` (source: src/main/resources/application.yml:resilience4j.circuitbreaker)

### Default Config (applied to all instances)

| Parameter | Value |
|---|---|
| Sliding window type | COUNT_BASED |
| Sliding window size | 6 |
| Min calls before tripping | 5 |
| Failure rate threshold | 50% |
| Permitted calls in half-open | 1 |
| Wait in open state | 30 s |
| Slow call duration threshold | 40 s |
| Auto-transition to half-open | true |
| Event consumer buffer | 5 |
| Recorded exception | `ExtendedExternalApiException` only |

(source: src/main/resources/application.yml:resilience4j.circuitbreaker.configs.default)

### Named Instances

All instances use `baseConfig: default`. Named instances:
`customerClientInstance`, `offerClientInstance`, `routingClientInstance`, `cargoWeightClientInstance`, `locationClientInstance`, `facilityClientInstance`, `commodityClientInstance`, `reeferTempClientInstance`, `containerTypeClientInstance`, `salesObjectClientInstance`, `ratesClientInstance`, `BASIC`, `customsClientInstance`

(source: src/main/resources/application.yml:resilience4j.circuitbreaker.instances)

## Health Monitoring

Circuit breaker health indicators are enabled: `management.health.circuitbreakers.enabled: true`
Exposed at `/actuator/health` with `show-details: always` (source: src/main/resources/application.yml:management.health)
