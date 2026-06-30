---
category: contracts
title: Kafka listeners and event contracts
summary: Local Kafka contracts are concentrated in vendor ingestion, with versioned topics, consumer groups, Avro payloads, and schema-registry-backed deserialization.
primary_for: [kafka-event-contracts]
mentions: [kafka, vendor, avro, consumer-group, schema-registry]
scenarios:
  - find kafka topic
  - find consumer group
  - inspect avro consumer
  - see vendor event flow
  - check kafka retries
capabilities: [event-contracts]
domains: [vendors, platform]
entities: [VendorDataConsumer, VendorV2DataConsumer, DedicatedKafkaConfig, VendorV2KafkaConfig]
sources:
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorDataConsumer.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorV2DataConsumer.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/DedicatedKafkaConfig.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/VendorV2KafkaConfig.kt
  - service/src/main/resources/application.yaml
  - service/src/main/kotlin/com/maersk/iom/master/data/IomMasterDataApplication.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - runtime/vendor-ingest-flow.md
  - integrations/vendor-feeds.md
  - operations/retries.md
---

# Kafka contracts

- V1 vendor ingest listens on `topicPattern = ${kafka.vendor.data.topic}` with `groupId = ${kafka.vendor.data.consumer.group}` and a `ConsumerRecord<String, Vendor>` payload. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorDataConsumer.kt:18)
- V2 vendor ingest listens on `topicPattern = ${kafka.vendor.v2.data.topic}` with `groupId = ${kafka.vendor.v2.data.consumer.group}` and a batched `List<ConsumerRecord<String, VendorMessage>>` payload. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorV2DataConsumer.kt:23)
- Both listener factories deserialize Avro through `KafkaAvroDeserializer` with `SPECIFIC_AVRO_READER_CONFIG=true`. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/DedicatedKafkaConfig.kt:117; vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/VendorV2KafkaConfig.kt:166)
- Schema registry URLs are supplied by `kafka.vendor.schema-registry.schemaRegistryUrl` for V1 and `kafka.vendor.v2.schema-registry.schemaRegistryUrl` for V2. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/DedicatedKafkaConfig.kt:42; vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/VendorV2KafkaConfig.kt:46)
- The application boot class also imports external shared Kafka wiring via `KafkaConfig` and `LocationDomainConfiguration`, but this repository does not declare any local non-vendor `@KafkaListener` methods. (source: service/src/main/kotlin/com/maersk/iom/master/data/IomMasterDataApplication.kt:25)
