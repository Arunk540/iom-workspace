---category: stack
title: Stack and build commands
summary: iom-order-service is a Kotlin 1.9 / Java 17 Spring Boot 3.1.5 reactive service using WebFlux, Spring Security, R2DBC PostgreSQL, Kafka Avro, Liquibase, Micrometer, and LaunchDarkly. Gradle Kotlin DSL drives builds, tests, Avro generation, linting, and image packaging.
primary_for: [stack-detection]
mentions: [gradle, kotlin, spring-boot, webflux, kafka, r2dbc]
scenarios:
  - detect framework stack
  - find build command
  - find test command
  - find runtime libraries
  - find db tooling
  - understand the tech stack
capabilities: [stack]
domains: [platform]
entities: [Gradle, SpringBoot, Kafka, PostgreSQL, Liquibase]
sources:
  - build.gradle.kts
  - gradle.properties
  - settings.gradle.kts
  - src/main/resources/application.yaml
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - architecture/modules.md
  - operations/monitoring.md
  - contracts/kafka-events.md
---
## Runtime stack
- Kotlin `1.9.22`, Java 17, Spring Boot `3.1.5`, Spring WebFlux, Spring Security OAuth2 resource server.
- PostgreSQL via R2DBC plus Liquibase migrations.
- Spring Kafka with Confluent Avro serializer/deserializer.
- Micrometer tracing/observation, Prometheus registry, Brave bridge.
- LaunchDarkly server SDK.
- MapStruct, Jolt, Apache POI.

## Build & test commands
- `./gradlew test`
- `./gradlew build`
- `./gradlew jacocoTestReport`
- `./gradlew sonarqube`
- `./gradlew bootRun`
- `./gradlew generateAvroJava`
- `./gradlew ktlintCheck` or `./gradlew ktlintFormat` if the plugin task set is enabled by the ktlint Gradle plugin.

## Notes
- `generateAvroJava` reads `src/main/resources/avro`.
- `bootRun` enables Reactor Netty access logging.
- Gradle caching and parallel execution are enabled in `gradle.properties`.
