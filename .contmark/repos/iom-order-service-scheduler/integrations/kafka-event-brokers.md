---
category: integrations
title: Kafka event brokers
summary: Outbox publishers send Avro records to Kafka using two bootstrap/server groups and schema-registry settings. Producer behavior is configured centrally in `KafkaProducerConfig`.
primary_for: [kafka-event-brokers]
mentions: [kafka-topic-contracts, avro-serialization, schema-registry-settings]
scenarios:
  - kafka broker config
  - schema registry issue
  - producer bootstrap servers
  - avro publish config
  - kafka ssl setup
capabilities: [outbox-publishing]
domains: [IOM]
entities: [KafkaTemplate, ProducerRecord]
peer_systems: [kafka, schema-registry]
direction: outbound
protocol: kafka
topic_or_endpoint: kafka.service-plan.topic; kafka.service-plan-domain-event.topic; kafka.external-event.topic
sources:
  - src/main/kotlin/com/maersk/iom/config/KafkaProducerConfig.kt
  - src/main/resources/application.yaml
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - contracts/kafka-events.md
  - operations/retries.md
---

# Kafka broker integration

- `KafkaProducerConfig` creates three `KafkaTemplate` beans: service plan, service plan domain event, and external event (source: src/main/kotlin/com/maersk/iom/config/KafkaProducerConfig.kt:170)
- `createKafkaProducerProperties(...)` routes `SERVICE_PLAN_STATUS_CHANGED` to `bootstrapServers` + `schemaRegistryUrl`, while `BOOKING_STATUS_CHANGED` and `SERVICE_PLAN_CHANGED` use `bootstrapServersDomainEvent` + `schemaRegistryUrlDomainEvent` (source: src/main/kotlin/com/maersk/iom/config/KafkaProducerConfig.kt:208)
- All producers use `StringSerializer` keys, `KafkaAvroSerializer` values, `acks`, retry backoff, delivery timeout, batch size, and `MAX_IN_FLIGHT_REQUESTS_PER_CONNECTION=1` (source: src/main/kotlin/com/maersk/iom/config/KafkaProducerConfig.kt:220)
- Base topic properties default to localhost values in `application.yaml`, with separate `kafka.serviceplandomainevent.*` settings for the shared cluster path (source: src/main/resources/application.yaml:211)
- When `kafka.saslRequired=true`, SSL truststore/keystore and schema-registry SSL settings are added; external-topic and service-plan-domain-event flows can override SASL JAAS config for shared-cluster publishing (source: src/main/kotlin/com/maersk/iom/config/KafkaProducerConfig.kt:144)
