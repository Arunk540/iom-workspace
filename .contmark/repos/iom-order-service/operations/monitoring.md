---category: operations
title: Monitoring and observability
summary: The service uses Micrometer observation, Prometheus metrics, tracing, MDC enrichment, access logs, and custom counters around booking and customer-history flows. Observability is configured in both code and application YAML.
primary_for: [monitoring-observability]
mentions: [micrometer, prometheus, tracing, mdc, custom-counters]
scenarios:
  - inspect tracing
  - inspect metrics
  - inspect prometheus
  - inspect access logs
  - inspect custom counters
  - find observability wiring
  - check observability setup
capabilities: [operations]
domains: [observability]
entities: [ObservationRegistry, MeterRegistry, TraceIdProvider, MDC]
sources:
  - src/main/resources/application.yaml
  - src/main/kotlin/com/maersk/iom/filter/ObservationWebFilter.kt
  - src/main/kotlin/com/maersk/iom/config/ObservedAspectConfiguration.kt
  - src/main/kotlin/com/maersk/iom/config/ObservationHandlerConfig.kt
  - src/main/kotlin/com/maersk/iom/messaging/BookingUpdatesEventHandler.kt
  - src/main/kotlin/com/maersk/iom/messaging/CustomerBookingHistoryEventConsumer.kt
  - build.gradle.kts
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - architecture/cross-cutting.md
  - operations/retries.md
  - operations/failure-model.md
---
- `ObservationWebFilter` adds `Trace-Id` to HTTP responses and wraps request processing in Micrometer observation. (source: src/main/kotlin/com/maersk/iom/filter/ObservationWebFilter.kt:13-25)
- `ObservedAspectConfiguration` activates `@Observed`, and `ObservationHandlerConfig` copies trace/span/parent IDs into MDC for logs. (source: src/main/kotlin/com/maersk/iom/config/ObservedAspectConfiguration.kt:8-15, src/main/kotlin/com/maersk/iom/config/ObservationHandlerConfig.kt:10-28)
- Application config exposes `health,info,prometheus,liquibase`, enables tracing with W3C propagation, and configures histogram/percentile collection for HTTP client/server requests. (source: src/main/resources/application.yaml:309-343)
- `build.gradle.kts` includes Micrometer core/tracing/observation, Brave bridge, and Prometheus registry dependencies. (source: build.gradle.kts:148-151, build.gradle.kts:207-208)
- Booking updates increment booking-topic counters for confirmed, cancelled, and default event paths. (source: src/main/kotlin/com/maersk/iom/messaging/BookingUpdatesEventHandler.kt:290-323)
- Customer-history consumer increments `customer_history_events_processed_success` and `customer_history_events_processed_error` counters tagged by topic and error type. (source: src/main/kotlin/com/maersk/iom/messaging/CustomerBookingHistoryEventConsumer.kt:408-417)
- `bootRun` turns on Reactor Netty access logs and YAML sets query-level debug logging for R2DBC PostgreSQL. (source: build.gradle.kts:262-265, src/main/resources/application.yaml:247-259)
