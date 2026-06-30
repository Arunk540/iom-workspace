---
category: stack
title: Technology stack
summary: The service is a Java 17 Maven build using Spring Boot WebFlux, Spring Security resource server, springdoc OpenAPI, and PostgreSQL via R2DBC.
primary_for: [facilities-reactive-stack]
mentions: [java17, maven, spring-boot, webflux, r2dbc, postgresql, springdoc, junit5, cucumber]
scenarios:
  - inspect tech stack
  - find build commands
  - find test commands
  - inspect reactive libraries
  - inspect data stack
capabilities: [stack-discovery]
domains: [smds-facilities]
entities: [SpringBoot, WebFlux, R2dbcEntityTemplate, PostgreSQL, Maven]
sources:
  - service/pom.xml
  - componenttest/pom.xml
  - README.md
  - service/docker-compose.yaml
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - architecture/modules.md
  - integrations/postgresql.md
---

# Technology stack

## Runtime stack

- Java 17 service built with Maven parent `telikos-parent`.
- Spring Boot + Spring WebFlux for reactive HTTP handling.
- Spring Security OAuth2 resource server with Telikos JWT validator.
- springdoc OpenAPI UI for generated API docs.
- PostgreSQL accessed through Spring Data R2DBC and `r2dbc-postgresql`.
- ModelMapper + Lombok for mapping and boilerplate reduction.

## Test stack

- `service/src/test` uses Spring Boot Test + JUnit 5.
- `componenttest/` uses Cucumber + Serenity and starts PostgreSQL plus the service container with Testcontainers.

## Local runtime helpers

- `service/docker-compose.yaml` provisions the service, PostgreSQL, and Redis containers for local work.
- `postgresql_scripts/` holds Liquibase-managed schema scripts.

## Build & test commands

- `cd service && mvn test`
- `cd service && mvn clean package`
- `cd service && mvn checkstyle:check`
- `cd service && mvn clean install`
- `cd componenttest && mvn test`
- `cd service && docker compose up --build`

## Related

- [[architecture/modules]]
- [[integrations/postgresql]]
