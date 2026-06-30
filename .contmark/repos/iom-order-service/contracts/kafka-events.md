---category: contracts
title: Kafka events
summary: The repository consumes three Kafka streams and writes outbound domain events to an outbox table instead of directly producing Kafka records. Topic patterns, group IDs, concurrency, and ack toggles are all property-driven.
primary_for: [kafka-contracts]
mentions: [booking-topic, service-plan-domain-events, customer-history, outbox]
scenarios:
  - list kafka topics
  - find consumer group
  - inspect ack config
  - inspect outbound events
  - inspect avro inputs
  - find kafka topics
capabilities: [contracts]
domains: [messaging]
entities: [Booking, EventNotification, ServicePlan, OutboxMessage]
sources:
  - src/main/resources/application.yaml
  - src/main/kotlin/com/maersk/iom/config/KafkaConfig.kt
  - src/main/kotlin/com/maersk/iom/messaging/BookingConsumer.kt
  - src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventConsumer.kt
  - src/main/kotlin/com/maersk/iom/messaging/CustomerBookingHistoryEventConsumer.kt
  - src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt
  - src/main/kotlin/com/maersk/iom/model/event/strategy/OutboxStrategy.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - runtime/booking-event-processing-flow.md
  - runtime/service-plan-domain-event-flow.md
  - operations/retries.md
---
## Inbound topics
- Booking events: `topicPattern=${kafka.booking.topic}` and `groupId=${kafka.booking.consumer.group}`; base YAML defaults to `^.*iom-booking-topic.local.v1*` and `iom-booking-topic-consumerGroup.local.v1`. (source: src/main/kotlin/com/maersk/iom/messaging/BookingConsumer.kt:26-31, src/main/resources/application.yaml:354-356, src/main/resources/application.yaml:411-421)
- Service-plan domain events: `topicPattern=${kafka.service-plan-domain-event.topic}` and `groupId=${kafka.service-plan-domain-event.consumer.group}`; base YAML defaults to `^.*iom-serviceplan-domain-event-topic.local.v1*` and `iom-serviceplan-domain-event-topic-consumerGroup.local.v1`. (source: src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventConsumer.kt:31-36, src/main/resources/application.yaml:356-371, src/main/resources/application.yaml:422-447)
- Customer booking history events: `topicPattern=${kafka.customer-booking-history-event.topic}` and `groupId=${kafka.customer-booking-history-event.consumer-group}`; base YAML defaults to `msk.ohptelikos.serviceplanpreprod.topic.local.any.v6` and `msk.ohptelikos.serviceplandev.consumergroup.v2`. (source: src/main/kotlin/com/maersk/iom/messaging/CustomerBookingHistoryEventConsumer.kt:45-50, src/main/resources/application.yaml:381-382, src/main/resources/application.yaml:448-452)

## Listener policy
- Booking and customer-history factories use the shared bootstrap servers and manual-immediate acknowledgment with auto-commit disabled. (source: src/main/kotlin/com/maersk/iom/config/KafkaConfig.kt:183-199, src/main/kotlin/com/maersk/iom/config/KafkaConfig.kt:210-287)
- Service-plan-domain-event listeners use a dedicated bootstrap/schema-registry property block and the same fixed-backoff error handler. (source: src/main/kotlin/com/maersk/iom/config/KafkaConfig.kt:112-146, src/main/kotlin/com/maersk/iom/config/KafkaConfig.kt:291-350)
- Concurrency defaults are 8 for booking and service-plan-domain-event listeners; customer-history reuses booking concurrency. (source: src/main/resources/application.yaml:416-417, src/main/resources/application.yaml:428-430, src/main/kotlin/com/maersk/iom/config/KafkaConfig.kt:274-295)

## Payload types
- Booking consumer deserializes Avro `Booking`. (source: src/main/kotlin/com/maersk/iom/config/KafkaConfig.kt:214-237, src/main/resources/avro/Booking.v8.avsc:1-20)
- Service-plan-domain-event consumer deserializes Avro `EventNotification`. (source: src/main/kotlin/com/maersk/iom/config/KafkaConfig.kt:318-350, src/main/kotlin/com/maersk/iom/messaging/ServicePlanDomainEventConsumer.kt:37-55)
- Customer-history consumer deserializes Avro `ServicePlan`. (source: src/main/kotlin/com/maersk/iom/config/KafkaConfig.kt:240-267, src/main/kotlin/com/maersk/iom/messaging/CustomerBookingHistoryEventConsumer.kt:51-61)

## Outbound event staging
- `OrderServiceImpl` creates `SERVICE_PLAN_CHANGED`, `SERVICE_PLAN_STATUS_CHANGED`, `BOOKING_STATUS_CHANGED`, and `REPRICING_REQUIRED` domain events during save flows. (source: src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:323-349)
- `OutboxStrategy` writes those events to `outbox_messages` with `key=servicePlanNumber`, `type=eventType`, `payload=json`, `priority`, and `status=PENDING`; this repo does not contain a direct Kafka producer/topic binding for the outbox rows. (source: src/main/kotlin/com/maersk/iom/model/event/strategy/OutboxStrategy.kt:25-35)
