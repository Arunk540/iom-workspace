---
category: runtime
title: Service-plan domain-event flow
summary: Service-plan domain-event Kafka ingress is audit-focused in this repository. The consumer persists `SERVICE_PLAN_CHANGED` snapshots and JSON diffs into the `audits` table and ignores other event types beyond returning the service-plan number.
primary_for: [service-plan-domain-events]
mentions: [domain-event-consumer, audit-service, audits-table, event-diff]
scenarios:
  - debug domain event audit
  - inspect service plan changed
  - inspect audit diff
  - inspect ack behavior
  - inspect event filter
capabilities: [runtime-flow]
domains: [audit]
entities: [EventNotification, AuditEntity, ServicePlanChanged]
sources:
  - src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventConsumer.kt
  - src/main/kotlin/com/maersk/iom/service/audit/AuditService.kt
  - src/main/resources/db/dbchangelog.xml
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - contracts/kafka-events.md
  - contracts/db-schemas.md
  - operations/failure-model.md
---
1. `ServicePlanDomainEventConsumer.listenForServicePlanNotification` listens on `kafka.service-plan-domain-event.topic`, builds event context from topic/offset, and forwards the payload to `AuditService.audit(...)`. (source: src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventConsumer.kt:31-55)
2. The consumer acknowledges only when `kafka.service-plan-domain-event.consumer.acknowledgementReq` is true; it also wraps the chain in Micrometer observation and blocks on `.toFuture().get()`. (source: src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventConsumer.kt:28-29, src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventConsumer.kt:67-71)
3. `AuditService.audit(...)` short-circuits non-`SERVICE_PLAN_CHANGED` notifications by returning the service-plan number without an audit insert. (source: src/main/kotlin/com/maersk/iom/service/audit/AuditService.kt:20-23)
4. For changed events, the service reads the latest audit row, rejects out-of-order older events, computes a JSON diff against the previous payload, and saves a new `AuditEntity`. (source: src/main/kotlin/com/maersk/iom/service/audit/AuditService.kt:30-60, src/main/kotlin/com/maersk/iom/service/audit/AuditService.kt:66-84)
5. The `audits` table stores service-plan number, event type, source, sender, booking number, product type, tracing id, event timestamp, full payload JSON, and diff JSON. (source: src/main/resources/db/dbchangelog.xml:220-238)
