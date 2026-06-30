---category: contracts
title: Database schemas
summary: PostgreSQL persistence centers on `service_plans`, `audits`, `outbox_messages`, `incidents`, and `customer_bookings_history`. Query paths use R2DBC against denormalized search columns and a JSONB-backed ServicePlan payload.
primary_for: [db-contracts]
mentions: [service_plans, audits, outbox_messages, incidents, customer_bookings_history]
scenarios:
  - list core tables
  - inspect search columns
  - inspect outbox schema
  - inspect audit schema
  - inspect customer history table
  - find the api contracts
  - check contracts definitions
capabilities: [contracts]
domains: [persistence]
entities: [ServicePlanEntity, AuditEntity, OutboxMessage, IncidentEntity]
sources:
  - src/main/resources/application.yaml
  - src/main/resources/db/dbchangelog.xml
  - src/main/resources/db/v3dbchangelog.xml
  - src/main/resources/db/v4dbchangelog.xml
  - src/main/resources/db/v5dbchangelog.xml
  - src/main/resources/db/v7dbchangelog.xml
  - src/main/resources/db/v8dbchangelog.xml
  - src/main/kotlin/com/maersk/iom/service/QueryService.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - domain/service-plan.md
  - operations/failure-model.md
  - integrations/master-data-and-rules.md
---
- Connectivity uses PostgreSQL over R2DBC plus Liquibase with schema `${postgresschema}`. (source: src/main/resources/application.yaml:166-184)
- `service_plans` started as `service_plan_number` + `data jsonb`; later migrations added timestamps, denormalized search columns (`booked_by`, `price_owner`, `facility_country_code`, `products`, `pickup_city`, `destination_city`), lock/versioning, customs/closure flags, FinOps, VAT/cash flags, `container_numbers`, `order_type`, `site_code`, `iso_container_type_codes`, and `customer_history_status`. (source: src/main/resources/db/v5dbchangelog.xml:11-19, src/main/resources/db/dbchangelog.xml:210-218, src/main/resources/db/dbchangelog.xml:275-304, src/main/resources/db/v3dbchangelog.xml:6-18, src/main/resources/db/v4dbchangelog.xml:12-37, src/main/resources/db/v5dbchangelog.xml:13-21, src/main/resources/db/v8dbchangelog.xml:6-59)
- `QueryService` pages `ServicePlanEntity` rows through R2DBC and runs raw SQL overlap searches on `service_plans.container_numbers`. (source: src/main/kotlin/com/maersk/iom/service/QueryService.kt:32-48, src/main/kotlin/com/maersk/iom/service/QueryService.kt:63-119)
- `audits` stores service-plan audit snapshots and JSON diffs, indexed by `service_plan_number`. (source: src/main/resources/db/dbchangelog.xml:220-243)
- `outbox_messages` stores deferred domain-event delivery state with `key`, `type`, `payload`, retry/error metadata, `priority`, and `status`. (source: src/main/resources/db/dbchangelog.xml:257-304, src/main/resources/db/v7dbchangelog.xml:6-38)
- `incidents` stores customer/business/technical exception rows keyed by service-plan number with codes, message, type, active flag, and audit timestamps. (source: src/main/resources/db/dbchangelog.xml:25-83)
- `customer_bookings_history` stores one JSONB aggregate per `customer_id` with created/modified timestamps. (source: src/main/resources/db/v5dbchangelog.xml:36-48)
- `archival_meta_data` exists for archival bookkeeping with key/entity/url/error metadata. (source: src/main/resources/db/dbchangelog.xml:6-18)
