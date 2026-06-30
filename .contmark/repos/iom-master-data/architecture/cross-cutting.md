---category: architecture
title: Cross-cutting runtime architecture
summary: Spring WebFlux, R2DBC, Liquibase, security, observability, and shared Kafka wiring shape every domain module in iom-master-data.
primary_for: [cross-cutting-runtime-rules]
mentions: [security, liquibase, observability, kafka, launchdarkly, webflux]
scenarios:
  - understand shared config
  - see security posture
  - trace liquibase strategy
  - see monitoring hooks
  - find feature flag wiring
  - find runtime rules
  - check runtime behavior
capabilities: [cross-cutting-architecture]
domains: [platform]
entities: [SecurityConfig, ObservedAspectConfiguration, dbchangelogmaster.xml]
sources:
  - service/src/main/kotlin/com/maersk/iom/master/data/config/SecurityConfig.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/config/ObservedAspectConfiguration.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/config/LaunchDarklyLocalConfig.kt
  - service/src/main/resources/application.yaml
  - service/src/main/resources/db/dbchangelogmaster.xml
  - liquibase.properties
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - stack/stack.md
  - operations/monitoring.md
  - operations/retries.md
---

# Shared concerns

- Security is centralized in `SecurityConfig`, which enables WebFlux security, disables CSRF, permits Swagger paths, and currently permits all other exchanges. 
- Liquibase runs from `dbchangelogmaster.xml`, which includes both `dbchangelog.xml` and `dbrunonchangelog.xml`, and uses the `iom` schema from `liquibase.properties`.
- Observability is active through `ObservedAspectConfiguration`, Micrometer Prometheus dependencies, Actuator endpoint exposure, and Zipkin tracing properties.
- Local feature-flag testing is supported through `LaunchDarklyLocalConfig`, which wires `LDReactorClient` from `FeatureCatalog.json` only for the `local` profile.
- Shared Kafka wiring is imported at boot from external `KafkaConfig` and `LocationDomainConfiguration`, while this repo adds dedicated vendor listener factories.
