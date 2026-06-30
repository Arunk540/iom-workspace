---category: stack
title: Technology stack and commands
summary: iom-master-data is a Kotlin 2.0 / Spring Boot 3.3 reactive Gradle monorepo with R2DBC Postgres, Liquibase, Kafka, Micrometer, and Kotest/JUnit validation.
primary_for: [service-stack-and-commands]
mentions: [kotlin, spring-boot, gradle, r2dbc, liquibase, kafka, kotest]
scenarios:
  - see tech stack
  - find build command
  - find test command
  - see framework versions
  - see runtime dependencies
  - understand the tech stack
capabilities: [stack-summary]
domains: [platform]
entities: [build.gradle.kts, service/build.gradle.kts]
sources:
  - build.gradle.kts
  - service/build.gradle.kts
  - service/src/main/resources/application.yaml
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - architecture/modules.md
  - architecture/cross-cutting.md
  - operations/monitoring.md
---

# Stack summary

- Kotlin `2.0.0` on JVM 17 across the repo.
- Spring Boot `3.3.13` with WebFlux, validation, security, actuator, and springdoc OpenAPI.
- Reactive persistence via Spring Data R2DBC and PostgreSQL (`r2dbc-postgresql` + JDBC driver for Liquibase).
- Liquibase migrations in the `service` module with `liquibase-sessionlock` to avoid stuck locks.
- Kafka consumers in vendor modules using Confluent Avro deserializers and schema registry settings.
- Micrometer Prometheus, Zipkin tracing, and `ObservedAspect` for telemetry.
- LaunchDarkly local file-backed client for profile-based flag testing.
- Testing uses JUnit 5, Kotest, Reactor Test, Spring Kafka Test, and Testcontainers.
- Static analysis/linting uses ktlint, JaCoCo aggregation, SonarQube, and dependency analysis.

## Build & test commands

- `./gradlew compileKotlin`
- `./gradlew test`
- `./gradlew ktlintCheck`
- `./gradlew build`
- `./gradlew jacocoTestReport`
- `./gradlew update -p service` for Liquibase updates when DB credentials are supplied.
