---category: operations
title: Monitoring
summary: Observability combines actuator endpoints, Micrometer observations/counters, Zipkin tracing, and JSON logs with trace/span IDs. Scheduler and HTTP timing are logged with elapsed-time helpers.
primary_for: [observability-wiring]
mentions: [actuator-endpoints, micrometer-observation, zipkin-tracing]
scenarios:
  - tracing setup
  - prometheus endpoint
  - scheduler metrics
  - log correlation
  - monitoring config
  - find observability wiring
  - check observability setup
capabilities: [operations]
domains: [IOM]
entities: [ObservationRegistry, MeterRegistry, SecurityWebFilterChain]
sources:
  - src/main/kotlin/com/maersk/iom/IomOrderSchedulerApplication.kt
  - src/main/kotlin/com/maersk/iom/messaging/ServicePlanEventsProducer.kt
  - src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventsProducer.kt
  - src/main/kotlin/com/maersk/iom/messaging/BookingStatusChangedProducer.kt
  - src/main/kotlin/com/maersk/iom/offer/OfferScheduler.kt
  - src/main/kotlin/com/maersk/iom/offer/OfferClient.kt
  - src/main/kotlin/com/maersk/iom/offer/WebIntegratorClient.kt
  - src/main/kotlin/com/maersk/iom/config/SecurityConfig.kt
  - src/main/resources/application.yaml
  - src/main/resources/logback.xml
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - architecture/cross-cutting.md
  - operations/failure-model.md
---

# Monitoring and tracing

- `main()` enables Reactor automatic context propagation before Spring startup, allowing trace context to cross reactive boundaries (source: src/main/kotlin/com/maersk/iom/IomOrderSchedulerApplication.kt:18)
- Actuator exposes `health`, `info`, `prometheus`, and `liquibase`, and health details are always shown (source: src/main/resources/application.yaml:153)
- `SecurityConfig` permits unauthenticated `GET /actuator/**`, which is the path used by health and metrics scrapers (source: src/main/kotlin/com/maersk/iom/config/SecurityConfig.kt:18)
- Tracing is enabled with sampling probability `1.0`; Zipkin export endpoint is configured under `management.zipkin.tracing.endpoint` (source: src/main/resources/application.yaml:170)
- Logback emits JSON logs containing `traceID` and `spanID` fields (source: src/main/resources/logback.xml:2)
- Kafka producers use `@Observed` plus `.tap(Micrometer.observation(registry))`, so producer chains participate in Micrometer observation (source: src/main/kotlin/com/maersk/iom/messaging/ServicePlanEventsProducer.kt:28; source: src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventsProducer.kt:26; source: src/main/kotlin/com/maersk/iom/messaging/BookingStatusChangedProducer.kt:29)
- `ServicePlanEventsProducer` and `BookingStatusChangedProducer` increment custom success/failure counters tagged with topic name (source: src/main/kotlin/com/maersk/iom/messaging/ServicePlanEventsProducer.kt:95; source: src/main/kotlin/com/maersk/iom/messaging/BookingStatusChangedProducer.kt:96)
- `OfferScheduler`, `OfferedServicePlanClientImpl`, and `WebIntegratorClientImpl` wrap calls with `logElapsedTime(...)` for latency logging (source: src/main/kotlin/com/maersk/iom/offer/OfferScheduler.kt:25; source: src/main/kotlin/com/maersk/iom/offer/OfferClient.kt:31; source: src/main/kotlin/com/maersk/iom/offer/WebIntegratorClient.kt:59)
