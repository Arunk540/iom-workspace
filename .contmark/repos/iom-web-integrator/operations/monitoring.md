---
category: operations
title: Monitoring and observability
summary: Actuator, Prometheus, Micrometer tracing, outbound request observation, and the notification health aggregator define the service’s operational visibility.
primary_for: [monitoring-setup]
mentions: [monitoring, metrics, prometheus, tracing, health]
scenarios:
  - monitoring setup
  - prometheus endpoints
  - tracing config
  - health endpoint setup
  - observability debug
capabilities: [operations-monitoring]
domains: [Operations]
entities: [ServiceHealthResponse, WebClientRequestObservation]
sources:
  - src/main/resources/application.yml
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/WebClientRequestObservation.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/ExternalServiceHealthService.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - architecture/cross-cutting.md
  - runtime/notification-health-flow.md
---
# Monitoring and observability

- Actuator exposes `health`, `info`, and `prometheus` over HTTP. (source: src/main/resources/application.yml:123)
- Health details are always shown, with a dedicated liveliness group for `diskSpace` and `ping`. (source: src/main/resources/application.yml:130)
- HTTP server and client metrics both publish percentile histograms, and tracing is enabled with W3C propagation plus Zipkin export. (source: src/main/resources/application.yml:112)
- `WebClientRequestObservation` overrides Micrometer’s URI tag extraction so outbound metrics keep the actual request path. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/WebClientRequestObservation.kt:13)
- `ExternalServiceHealthService` produces per-service response time and error detail, which is reused by the `/notification` endpoint. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/ExternalServiceHealthService.kt:23)
