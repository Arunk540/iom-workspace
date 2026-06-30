---
category: stack
title: Stack and build commands
summary: The service is Kotlin 1.9 on Java 17, Spring Boot 3.2 WebFlux, MapStruct, OpenAPI generation, Micrometer, Resilience4j, and optional Infinispan/LaunchDarkly integrations.
primary_for: [stack-overview]
mentions: [kotlin, spring, gradle, webflux, mapstruct]
scenarios:
  - stack overview
  - stack commands
  - build stack
  - test stack
  - framework overview
capabilities: [stack]
domains: [Web Integrator]
entities: [Spring Boot, Gradle, MapStruct]
sources:
  - build.gradle.kts
  - settings.gradle.kts
  - src/main/resources/application.yml
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - architecture/modules.md
  - operations/monitoring.md
---
# Stack

- Spring Boot `3.2.5` with WebFlux, Security, Validation, OAuth2 resource server/client, and Actuator. (source: build.gradle.kts:6)
- Kotlin `1.9.23` on JVM 17 with kapt-based MapStruct generation. (source: build.gradle.kts:9)
- OpenAPI Generator produces web-integrator, offer, order, and billing models into `build/generated/src`. (source: build.gradle.kts:132)
- Observability uses Micrometer observation/tracing plus Prometheus registry and Zipkin export. (source: build.gradle.kts:80)
- Resilience uses Resilience4j circuit breaker + reactor adapters. (source: build.gradle.kts:100)
- Optional infrastructure includes LaunchDarkly server SDK and Infinispan remote cache starter. (source: build.gradle.kts:98)
- Runtime is a reactive web application with 5 MB codec buffer and JSON-only defaults. (source: src/main/resources/application.yml:149)

## Build & test commands

- `./gradlew test` — unit and integration tests. (source: build.gradle.kts:117)
- `./gradlew ktlintCheck` — Kotlin lint. (source: build.gradle.kts:203)
- `./gradlew build` — compile, generate sources, tests, packaging. (source: build.gradle.kts:176)
- `./gradlew openApiGenerateAll` — regenerate downstream models. (source: build.gradle.kts:176)
- `./gradlew jib` — build container image. (source: build.gradle.kts:47)
