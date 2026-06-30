---category: contracts
title: Database schemas
summary: Runtime code is wired to `facility.facility_data_v3`, while checked-in Liquibase scripts create earlier `facility_data` and `facility_data_v2` generations.
primary_for: [facilities-db-schema]
mentions: [facility_data_v3, jsonb, liquibase, facility_source_data, status_code]
scenarios:
  - inspect db schema
  - inspect jsonb columns
  - inspect table version drift
  - inspect active status filter
  - inspect source data tables
  - find the db schema
capabilities: [contract-analysis]
domains: [smds-facilities]
entities: [FacilityData, facility_data, facility_data_v2, facility_data_v3]
sources:
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/contract/database/FacilityData.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/common/constants/QueryConstant.java
  - postgresql_scripts/dbchangelog-master.xml
  - postgresql_scripts/scripts/20230327_1_create_tables.sql
  - postgresql_scripts/scripts/20241127_1_create_tables.sql
  - componenttest/src/test/java/net/apmoller/crb/telikos/microservices/smds/facilities/util/InsertDataToDB.java
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - integrations/postgresql.md
  - runtime/facility-search-flow.md
---

# Database schemas

- The runtime row contract maps to schema `facility`, table `facility_data_v3`, with columns `facility_id`, `facility_type`, `status_code`, geo/RKST IDs, and `facility_data` JSONB. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/infrastructure/contract/database/FacilityData.java:25-53)
- Query constants also point the dynamic criteria path at `facility.facility_data_v3` and query nested JSONB fields for city, country, region, street, building number, facility name, postal code, and facility categories. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/common/constants/QueryConstant.java:10-40)
- The checked-in Liquibase master includes every SQL file under `postgresql_scripts/scripts/`. (source: postgresql_scripts/dbchangelog-master.xml:12-14)
- The older `20230327_1_create_tables.sql` script creates `facility.facility_data` and `facility.facility_source_data`, both with `facility_id`, `facility_type`, `status_code`, JSONB payloads, and timestamps. (source: postgresql_scripts/scripts/20230327_1_create_tables.sql:8-35)
- The newer `20241127_1_create_tables.sql` script creates `facility.facility_data_v2` and `facility.facility_source_data_v2` with the same broad shape but a v2 suffix. (source: postgresql_scripts/scripts/20241127_1_create_tables.sql:8-34)
- Component-test seed logic inserts directly into `facility.facility_data_v3`, confirming that test runtime expects a third-generation table not created by the checked-in DDL scripts shown above. (source: componenttest/src/test/java/net/apmoller/crb/telikos/microservices/smds/facilities/util/InsertDataToDB.java:20-32)

## Related

- [[integrations/postgresql]]
- [[runtime/facility-search-flow]]
