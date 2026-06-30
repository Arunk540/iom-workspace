---category: operations
title: Monitoring and observability
summary: Actuator, Micrometer, Zipkin tracing, observed AOP, and Kafka observation provide the main runtime telemetry surface.
primary_for: [monitoring-surface-map]
mentions: [actuator, prometheus, zipkin, observed, metrics]
scenarios:
  - find prometheus endpoint
  - inspect tracing
  - see observed aspect
  - find actuator exposure
  - see kafka observation
  - find monitoring setup
  - check monitoring config
capabilities: [operations-guide]
domains: [platform, vendors]
entities: [ObservedAspectConfiguration, SecurityConfig, DedicatedKafkaConfig, VendorV2KafkaConfig]
sources:
  - service/src/main/resources/application.yaml
  - service/src/main/kotlin/com/maersk/iom/master/data/config/ObservedAspectConfiguration.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/DedicatedKafkaConfig.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/VendorV2KafkaConfig.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - operations/retries.md
  - architecture/cross-cutting.md
  - stack/stack.md
---

# Monitoring surface

- Actuator exposes `health`, `info`, `prometheus`, `mappings`, and `liquibase` over HTTP. (source: service/src/main/resources/application.yaml:109)
- HTTP server and client requests both publish percentile histograms, and tracing is enabled with Zipkin endpoint `${zipkinbaseurl}`. (source: service/src/main/resources/application.yaml:99; service/src/main/resources/application.yaml:129)
- `ObservedAspectConfiguration` registers Micrometer's `ObservedAspect`, enabling `@Observed`-style timing and tracing hooks where used. (source: service/src/main/kotlin/com/maersk/iom/master/data/config/ObservedAspectConfiguration.kt:8)
- Vendor Kafka listener containers enable observation at the container level for both V1 and V2 consumers. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/DedicatedKafkaConfig.kt:88; vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/VendorV2KafkaConfig.kt:124)
