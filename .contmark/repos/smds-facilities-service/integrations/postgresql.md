---category: integrations
title: PostgreSQL storage
summary: The main runtime dependency is PostgreSQL over R2DBC, with JSONB-heavy query patterns and separate component-test seeding.
primary_for: [facilities-postgresql-storage]
mentions: [postgresql, r2dbc, jsonb, facility_data_v3, testcontainers]
scenarios:
  - inspect postgres integration
  - inspect r2dbc setup
  - inspect jsonb queries
  - inspect local database setup
  - inspect component test database
  - find postgresql storage
  - check postgresql config
capabilities: [integration-analysis]
domains: [smds-facilities]
entities: [FacilityData, R2dbcEntityTemplate, PostgreSQLContainer]
sources:
  - service/pom.xml
  - service/src/main/resources/application.yml
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/connector/DbConnector.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/repository/FacilitiesReactiveRepository.java
  - componenttest/src/test/java/TestRunner.java
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - contracts/db-schemas.md
  - stack/stack.md
---

# PostgreSQL storage

- Maven includes `spring-boot-starter-data-r2dbc` and `org.postgresql:r2dbc-postgresql`, making PostgreSQL the primary runtime datastore. (source: service/pom.xml:28-43)
- The default profile reads `spring.r2dbc.url`, `username`, and `password` from environment variables and enables a named R2DBC pool with max size 40. (source: service/src/main/resources/application.yml:68-76)
- Search queries use `R2dbcEntityTemplate` plus dynamic `Criteria` against nested JSONB fields, always adding `facility_type` and active `status_code`. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/connector/DbConnector.java:23-97)
- Fixed repository queries use JSONB operators for facility code, customer-code arrays, fuzzy facility-name matching, and OPS facility-category lookup. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/repository/FacilitiesReactiveRepository.java:36-89)
- Component tests start a `PostgreSQLContainer`, initialize schema from `postgres/create_table.sql`, and wire the service with `REACTIVE_POSTGRES_*` environment variables over a shared Docker network. (source: componenttest/src/test/java/TestRunner.java:32-90)

## Related

- [[contracts/db-schemas]]
- [[stack/stack]]
