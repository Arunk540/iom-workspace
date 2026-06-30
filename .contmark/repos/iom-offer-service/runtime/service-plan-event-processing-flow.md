---
category: runtime
title: Service Plan Event Processing Flow
summary: Kafka consumer flow linking offered plan to booked service plan number
primary_for: [kafka-event-processing-flow, service-plan-number-update-trace]
mentions: [ServicePlanDomainEventConsumer, OfferedServicePlanPersistenceService, EventNotification, KafkaConfig]
scenarios:
  - trace kafka event to mongodb update
  - understand service plan number linking
  - find kafka consumer configuration
  - understand acknowledgement behaviour
  - find offset reset strategy
capabilities: [kafka-consumption, persistence]
domains: [offer-management]
entities: [OfferedServicePlanDocument, EventNotification]
sources:
  - src/main/kotlin/com/maersk/iom/offer/messaging/ServicePlanDomainEventConsumer.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/OfferedServicePlanPersistenceService.kt
  - src/main/kotlin/com/maersk/iom/offer/config/KafkaConfig.kt
  - src/main/resources/avro/ServicePlanDomainEvent.v3.avsc
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [contracts/kafka-events.md, runtime/offer-search-flow.md, operations/failure-model.md]
---

## Trigger

Booking system publishes an `EventNotification` Avro message to the Kafka topic `${kafka.service-plan-domain-event.topic}` after a service plan is created/confirmed (source: src/main/resources/avro/ServicePlanDomainEvent.v3.avsc)

## Consumer Configuration

| Property | Value |
|---|---|
| Topic pattern | `${kafka.service-plan-domain-event.topic}` (default: `iom-serviceplan-domain-event-topic.local.v1`) |
| Consumer group | `${kafka.service-plan-domain-event.consumer.group}` |
| Container factory | `kafkaListenerContainerFactoryForServicePlanDomainEvent` |
| Auto startup | `${kafka.service-plan-domain-event.consumer.autoStartup}` |
| Max poll records | 1 (source: src/main/resources/application.yml:maxpollrecords) |
| Concurrency | 1 (source: src/main/resources/application.yml:consumerconcurrency) |
| Offset reset | `earliest` |
| Error backoff | max interval=100ms, max retries=3 (source: src/main/resources/application.yml:serviceplan-kafka-consumer-error) |

## Processing Steps

1. Receive `ConsumerRecord<String, EventNotification>` (source: src/main/kotlin/com/maersk/iom/offer/messaging/ServicePlanDomainEventConsumer.kt:34)
2. Extract `notification.message.servicePlanNumber` and `notification.message.offeredServicePlanNumber`
3. Call `OfferedServicePlanPersistenceService.updateServicePlanNumber(servicePlanNumber, offeredServicePlanNumber)` (source: src/main/kotlin/com/maersk/iom/offer/messaging/ServicePlanDomainEventConsumer.kt:44)
4. `updateServicePlanNumber` uses `ReactiveMongoTemplate.updateFirst()` to set `servicePlanNumber` field on `offered_service_plans` document matched by `offeredServicePlanNumber` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/OfferedServicePlanPersistenceService.kt:53)
5. On success: optionally acknowledge if `acknowledgementReqFlag` is true (source: src/main/kotlin/com/maersk/iom/offer/messaging/ServicePlanDomainEventConsumer.kt:49)
6. On error: logs error; Kafka error handler retries with backoff

## Avro Schema Summary

`EventNotification` record (namespace `com.maersk.iom`) contains:
- `header` — `messageId`, `eventTimestamp`
- `data` — `messageType`
- `message` — `servicePlanNumber`, `offeredServicePlanNumber` (source: src/main/resources/avro/ServicePlanDomainEvent.v3.avsc)

## Observability

`ServicePlanDomainEventConsumer` annotated `@Observed(name="ServicePlanDomainEventConsumer")`; each message processed uses `Micrometer.observation(registry)` tap on the reactive chain (source: src/main/kotlin/com/maersk/iom/offer/messaging/ServicePlanDomainEventConsumer.kt:17)

## Effect

After this flow, `OfferedServicePlanDocument.servicePlanNumber` transitions from `null` to the confirmed booking service plan number, making the offered plan eligible for downstream consumption and removing it from cleanup scope.
