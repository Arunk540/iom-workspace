---category: stack
title: Stack
summary: Kotlin 17 Spring Boot WebFlux service that runs scheduled jobs, publishes Kafka Avro events, talks to Temporal, PostgreSQL/R2DBC, Azure Blob Storage, and OAuth-protected HTTP services.
primary_for: [repository-tech-stack]
mentions: [gradle-build-commands, temporal-spring-boot, kafka-avro-publishing]
scenarios:
  - tech stack
  - build commands
  - test commands
  - what frameworks used
  - how to compile
  - understand the tech stack
capabilities: [stack]
domains: [IOM]
entities: [WorkflowClient, KafkaTemplate, BlobContainerClient]
sources:
  - build.gradle.kts
  - settings.gradle.kts
  - src/main/resources/application.yaml
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - architecture/cross-cutting.md
  - operations/monitoring.md
---

# Stack

- **Language/runtime**: Kotlin JVM 17 (`kotlin("jvm")`, `java.sourceCompatibility = 17`).
- **Frameworks**: Spring Boot 3.1.5, WebFlux, Data R2DBC, Actuator, AOP, Security OAuth2 client.
- **Workflow engine**: `io.temporal:temporal-spring-boot-starter:1.31.0`.
- **Messaging**: Spring Kafka + Confluent Avro serializer; generated schemas from `src/main/resources/avro`.
- **Persistence**: PostgreSQL via R2DBC and `r2dbc-proxy`; repositories/entities come from shared IOM libraries.
- **Storage**: Azure Blob Storage SDK.
- **Observability**: Micrometer Prometheus, Micrometer tracing Brave, Zipkin reporter, logstash-logback encoder.
- **Feature flags**: LaunchDarkly server SDK with local-file profile support.
- **Mapping/serialization**: MapStruct, Gson, Jackson Kotlin module, Kotlin serialization, Jolt.
- **Build tool**: single-module Gradle Kotlin DSL project named `iom-order-service-scheduler`.

## Build & test commands

- build: `./gradlew build` (source: build.gradle.kts:6)
- unit_test: `./gradlew test` (source: build.gradle.kts:170)
- component_test: `none` (source: build.gradle.kts:89)
- lint: `./gradlew ktlintCheck` (source: build.gradle.kts:168)
- typecheck: `./gradlew compileKotlin` (source: build.gradle.kts:162)
- verify: `./gradlew check` (source: build.gradle.kts:170)
