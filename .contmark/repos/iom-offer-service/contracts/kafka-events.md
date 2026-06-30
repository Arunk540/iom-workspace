---
category: contracts
title: Kafka Events
summary: Kafka topics consumed and their Avro message schema
primary_for: [kafka-event-contract-reference, avro-schema-guide]
mentions: [ServicePlanDomainEventConsumer, EventNotification, KafkaConfig]
scenarios:
  - understand kafka event schema
  - find topic and consumer group names
  - understand avro deserialization setup
  - trace message fields to mongo update
  - find kafka error handling config
capabilities: [kafka-consumption]
domains: [offer-management]
entities: [EventNotification, OfferedServicePlanDocument]
sources:
  - src/main/resources/avro/ServicePlanDomainEvent.v3.avsc
  - src/main/kotlin/com/maersk/iom/offer/messaging/ServicePlanDomainEventConsumer.kt
  - src/main/kotlin/com/maersk/iom/offer/config/KafkaConfig.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [runtime/service-plan-event-processing-flow.md, contracts/api-contracts.md, operations/failure-model.md]
---

## Consumed Topics

### Service Plan Domain Event

| Property | Value |
|---|---|
| Topic | `${service-plan-domain-event-kafka-topic}` (default: `iom-serviceplan-domain-event-topic.local.v1`) |
| Consumer group | `${consumerGroupServicePlanDomainEvent}` (default: `iom-serviceplan-domain-event-topic-consumerGroup.local.v1`) |
| Container factory | `kafkaListenerContainerFactoryForServicePlanDomainEvent` |
| Offset reset | `earliest` |
| Max poll records | 1 |
| Concurrency | 1 |
| Error backoff max interval | 100 ms |
| Error max retries | 3 |
(source: src/main/resources/application.yml:kafka.service-plan-domain-event)

## Avro Schema: EventNotification

Namespace: `com.maersk.iom`  
Schema file: `src/main/resources/avro/ServicePlanDomainEvent.v3.avsc`  
Generated class: `com.maersk.iom.EventNotification`

### Top-level Record Fields

| Field | Type | Description |
|---|---|---|
| `header` | `Header` record | Event metadata |
| `data` | `Data` record | Message type info |
| `message` | `Message` record | Payload with SP numbers |

### Header Record

| Field | Type | Notes |
|---|---|---|
| `messageId` | `string` | Unique event ID |
| `eventTimestamp` | `string` | ISO-8601 timestamp |

### Data Record

| Field | Type | Notes |
|---|---|---|
| `messageType` | `string` | Event type descriptor |

### Message Record

| Field | Type | Notes |
|---|---|---|
| `servicePlanNumber` | `string` | Booking system SP number |
| `offeredServicePlanNumber` | `string` | IOM offered plan number |

(source: src/main/resources/avro/ServicePlanDomainEvent.v3.avsc)

## Deserialization

Confluent Schema Registry used for Avro deserialization.  
Schema Registry URL: `${kafkaschemaregistryurl}` (source: src/main/resources/application.yml:kafkaschemaregistryurl)  
Authentication: SASL/PLAIN with `confluentkafkausername`/`confluentkafkapassword` when `kafkasaslRequired=true`

## No Produced Topics

`iom-offer-service` does **not** publish to any Kafka topic. It is a consumer-only service for the Service Plan Domain Event topic.
