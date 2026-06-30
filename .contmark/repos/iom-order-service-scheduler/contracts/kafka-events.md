---
category: contracts
title: Kafka events
summary: This repo publishes three outbound Kafka event shapes and does not implement traced Kafka consumers. Topics, payload schemas, and producer routing are configuration-driven.
primary_for: [kafka-topic-contracts]
mentions: [outbox-message-routing, kafka-event-brokers, avro-schema-mapping]
scenarios:
  - kafka topic contract
  - which topics publish
  - booking event topic
  - service plan topic
  - domain event topic
capabilities: [outbox-publishing]
domains: [IOM]
entities: [OutboxMessage, ServicePlan, EventNotification, BookingExternal]
sources:
  - src/main/kotlin/com/maersk/iom/outbox/scheduler/MessageProducerFactory.kt
  - src/main/kotlin/com/maersk/iom/messaging/ServicePlanEventsProducer.kt
  - src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventsProducer.kt
  - src/main/kotlin/com/maersk/iom/messaging/BookingStatusChangedProducer.kt
  - src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventMapper.kt
  - src/main/kotlin/com/maersk/iom/messaging/ServicePlanMapper.kt
  - src/main/kotlin/com/maersk/iom/messaging/BookingExternalMapper.kt
  - src/main/kotlin/com/maersk/iom/order/domain/extension/ReceiveChannelPartyExtension.kt
  - src/main/resources/application.yaml
  - src/main/resources/avro/ServicePlan.v8.avsc
  - src/main/resources/avro/ServicePlanDomainEvent.v4.avsc
  - src/main/resources/avro/BookingExternal.v1.avsc
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - integrations/kafka-event-brokers.md
  - navigation/entry-points.md
---

# Kafka topics and payloads

| Domain event type | Producer | Topic property | Payload shape |
|---|---|---|---|
| `SERVICE_PLAN_STATUS_CHANGED` | `ServicePlanEventsProducer` via `MessageProducerFactory` branch 1 (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/MessageProducerFactory.kt:19) | `kafka.service-plan.topic` → default `iom-serviceplan-topic.local.v1` (source: src/main/resources/application.yaml:262) | Avro record `com.maersk.ServicePlan` from `ServicePlan.v8.avsc` (source: src/main/resources/avro/ServicePlan.v8.avsc:4) |
| `SERVICE_PLAN_CHANGED` | `ServicePlanDomainEventsProducer` via `MessageProducerFactory` branch 2 (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/MessageProducerFactory.kt:23) | `kafka.service-plan-domain-event.topic` → default `iom-serviceplan-domain-event-topic.local.v1` (source: src/main/resources/application.yaml:266) | Avro record `com.maersk.iom.EventNotification` from `ServicePlanDomainEvent.v4.avsc` (source: src/main/resources/avro/ServicePlanDomainEvent.v4.avsc:4) |
| `BOOKING_STATUS_CHANGED` | `BookingStatusChangedProducer` via `MessageProducerFactory` branch 3 (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/MessageProducerFactory.kt:25) | `kafka.external-event.topic` → default `iom-booking-topic.internal.any.v1` (source: src/main/resources/application.yaml:270) | Avro record `com.maersk.bookingExternal.BookingExternal` from `BookingExternal.v1.avsc` (source: src/main/resources/avro/BookingExternal.v1.avsc:4) |

## Producer-side contract rules

- Unsupported outbox message types raise `Mono.error(Exception("Outbox message type ... not supported"))` in `MessageProducerFactory` (source: src/main/kotlin/com/maersk/iom/outbox/scheduler/MessageProducerFactory.kt:27)
- `ServicePlanEventsProducer` maps domain `ServicePlan` to Avro `ServicePlan` with `ServicePlanMapper.toServicePlanResponseForEmp(...)` before publish (source: src/main/kotlin/com/maersk/iom/messaging/ServicePlanEventsProducer.kt:58)
- `ServicePlanDomainEventsProducer` maps `DomainEvent`/`ServicePlanChanged` to `EventNotification` with header metadata and embedded message payload (source: src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventsProducer.kt:45; source: src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventMapper.kt:20)
- `BookingStatusChangedProducer` enriches booking references, overwrites AGENT parties for system channels, then maps to `BookingExternal` (source: src/main/kotlin/com/maersk/iom/messaging/BookingStatusChangedProducer.kt:55; source: src/main/kotlin/com/maersk/iom/order/domain/extension/ReceiveChannelPartyExtension.kt:11; source: src/main/kotlin/com/maersk/iom/messaging/BookingExternalMapper.kt:15)
- Each producer is feature-gated by an `*.active` property and becomes a no-op when disabled (source: src/main/kotlin/com/maersk/iom/messaging/ServicePlanEventsProducer.kt:41; source: src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventsProducer.kt:41; source: src/main/kotlin/com/maersk/iom/messaging/BookingStatusChangedProducer.kt:42)
