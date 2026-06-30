---category: contracts
title: Database and document schemas
summary: Summarizes the storage contracts implemented in iom-persistence across PostgreSQL tables and the Mongo order document.
primary_for: [storage-schema-contracts]
mentions: [service_plans table, outbox schema, audit table, mongo document]
scenarios:
  - find db schema
  - which tables exist
  - service plan entity shape
  - outbox columns
  - customer history table
  - find the api contracts
capabilities: [schema-documentation]
domains: [persistence]
entities: [ServicePlanEntity, OutboxMessage, AuditEntity, IncidentEntity]
sources:
  - iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/ServicePlanEntity.kt
  - iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/adapter/ServicePlanEntityRepository.kt
  - iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/OutboxMessage.kt
  - iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/AuditEntity.kt
  - iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/IncidentEntity.kt
  - iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/CustomerBookingsHistoryEntity.kt
  - iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/ArchivalMetaEntity.kt
  - iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/document/ServicePlanDocument.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - runtime/order-processing.md
  - operations/failure-model.md
---
## Main order storage

- `ServicePlanEntity` maps to the `service_plans` table and stores the primary key `service_plan_number`, current stage, work-process status, full serialized JSON payload in `data`, timestamps, party lookups, version, customs/finance flags, `lock_version`, container numbers, site code, and customer-history status. (source: iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/ServicePlanEntity.kt:19)
- `ServicePlanEntityRepository` exposes status queries, version backfill queries, optimistic update helpers for `finops_activities_completed` and `customer_history_status`, and search helpers for ocean reference, price owner, and missing container numbers. (source: iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/adapter/ServicePlanEntityRepository.kt:16)

## Operational tables

- `OutboxMessage` maps to `outbox_messages` with key, type, retry count, JSON payload, processed timestamp, retryability, last error message, priority, and status fields for transactional publishing. (source: iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/OutboxMessage.kt:15)
- `AuditEntity` maps to `audits` and captures event type, source, triggered-by user, booking number, tracing id, JSON payload, event timestamp, and optional JSON diff. (source: iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/AuditEntity.kt:11)
- `IncidentEntity` maps to `incidents` and stores customer-request or exception records by service-plan number, incident codes, type, message, active flag, and timestamps. (source: iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/IncidentEntity.kt:23)
- `CustomerBookingsHistoryEntity` maps to `customer_bookings_history` with `customer_id`, serialized JSON `data`, and created/modified timestamps. (source: iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/CustomerBookingsHistoryEntity.kt:13)
- `ArchivalMetaEntity` maps to `archival_meta_data` with key, entity class, archive URL, error text, and archive timestamp. (source: iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/ArchivalMetaEntity.kt:8)

## Document mirror

- `ServicePlanDocument` maps to Mongo collection `order` and mirrors the aggregate-rich shape: booking, legs, document pouch, product offer, routing, restrictions, version, and free-days data. (source: iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/document/ServicePlanDocument.kt:11)
