---category: operations
title: Monitoring and Observability
summary: Metrics, tracing, logging and health check configuration
primary_for: [monitoring-observability-reference, metrics-endpoint-guide]
mentions: [ObservationWebFilter, ObservationHandlerConfig, ObservedAspectConfiguration, SecurityConfig]
scenarios:
  - find prometheus metrics endpoint
  - understand distributed tracing setup
  - find health check endpoints
  - understand log level configuration
  - check zipkin tracing endpoint
  - find observability wiring
  - check observability setup
capabilities: [observe]
domains: [offer-management]
entities: []
sources:
  - src/main/resources/application.yml
  - src/main/kotlin/com/maersk/iom/offer/filter/ObservationWebFilter.kt
  - src/main/kotlin/com/maersk/iom/offer/config/ObservationHandlerConfig.kt
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [operations/retries.md, architecture/cross-cutting.md]
---

## Metrics (Prometheus)

Endpoint: `GET /actuator/prometheus` (source: src/main/resources/application.yml:management.endpoints.web.exposure)

### HTTP Metrics

Percentile histograms enabled for:
- `http.server.requests` — p90, p95, p99; max expected 5 s
- `http.client.requests` — p90, p95, p99

(source: src/main/resources/application.yml:management.metrics.distribution)

## Distributed Tracing

| Property | Value |
|---|---|
| Enabled | `true` |
| Sampling probability | `1.0` (100%) |
| Propagation | W3C TraceContext |
| Zipkin endpoint | `${zipkinbaseurl}` |

(source: src/main/resources/application.yml:management.tracing)

Micrometer Observation used throughout:
- `ObservationWebFilter` — per-request spans (source: src/main/kotlin/com/maersk/iom/offer/filter/ObservationWebFilter.kt)
- `@Observed(name="ServicePlanDomainEventConsumer")` on Kafka consumer (source: src/main/kotlin/com/maersk/iom/offer/messaging/ServicePlanDomainEventConsumer.kt:17)
- `logElapsedTime()` extension logs duration at DEBUG level for key operations

## Health Checks

Endpoints exposed: `health`, `info`, `prometheus` (source: src/main/resources/application.yml:management.endpoints.web.exposure.include)

Health groups:
- `liveliness`: `diskSpace`, `ping`
- All circuit breaker health indicators included

Shutdown endpoint enabled (`management.endpoint.shutdown.enabled: true`)

## Logging

| Logger | Level |
|---|---|
| `com.maersk.iom` | DEBUG |
| `reactor.netty.http.server.AccessLog` | INFO |
| `org.springframework.boot` | INFO |
| `org.springframework.data.mongodb.core.ReactiveMongoTemplate` | DEBUG |
| `io.github.resilience4j` | INFO |
| Root | WARN |

(source: src/main/resources/application.yml:logging.level)

Exception newlines replaced with unicode `\u2028` to prevent log injection (source: src/main/resources/application.yml:logging.exception-conversion-word)

## Application Info

Application name: `iom` (`spring.application.name`)  
Pod name tracked via `${POD_NAME}` (source: src/main/resources/application.yml:spring.application.pod-name)
