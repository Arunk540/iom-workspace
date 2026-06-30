---
category: stack
title: Stack and build commands
summary: Captures the framework stack, library packaging choices, and the smallest useful Gradle commands for this multi-module repo.
primary_for: [gradle-stack-profile]
mentions: [spring webflux, kotlin stack, gradle commands, library build]
scenarios:
  - how to build
  - run gradle tests
  - stack summary
  - what frameworks used
  - which java version
capabilities: [stack-summary]
domains: [iom-application]
entities: [Gradle, Spring Boot, WebFlux, R2DBC]
sources:
  - build.gradle.kts
  - application-model/build.gradle.kts
  - application-validators/build.gradle.kts
  - iom-common/build.gradle.kts
  - iom-order-domain/build.gradle.kts
  - iom-persistence/build.gradle.kts
  - reference-cache/build.gradle.kts
  - reference-data-client/build.gradle.kts
  - resolvers/build.gradle.kts
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - architecture/modules.md
---
## Core stack

- Kotlin `1.9.22` on JVM `17`
- Spring Boot `3.1.2`
- Spring WebFlux for non-blocking HTTP clients and service-facing models
- Spring Data R2DBC with PostgreSQL
- Spring Data Mongo Reactive for document persistence mirror
- Liquibase for schema tooling in root and persistence modules
- LaunchDarkly server SDK for feature flags
- Reactor + Micrometer tracing/observation
- JUnit 5, Kotest, Mockk, Mockito, Testcontainers, ArchUnit
- ktlint and dependency-analysis plugins

## Packaging profile

- Root project publishes an `application` java component and aggregates Jacoco across all submodules.
- Root `bootJar` is disabled, so the repo is packaged as reusable libraries rather than a runnable boot app.
- Most submodules publish sources and javadocs jars to GitHub Packages.

## Build & test commands

```bash
./gradlew build
./gradlew ktlintCheck
./gradlew analyzeClassesDependencies
./gradlew :application-model:test
./gradlew :application-validators:test
./gradlew :iom-common:test
./gradlew :iom-order-domain:test
./gradlew :iom-persistence:test
./gradlew :reference-cache:test
./gradlew :reference-data-client:test
./gradlew :resolvers:test
```

## Notes

- The root `test` task is disabled, so module-scoped tests are the practical verification unit.
- GitHub Packages credentials are loaded from `github.properties` or environment variables in each module build.
