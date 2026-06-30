---
category: runtime
title: Vendor Kafka ingest flow
summary: How V1 and V2 vendor consumers receive Kafka payloads, persist source payloads, and upsert normalized vendor rows.
primary_for: [vendor-kafka-ingest-flow]
mentions: [vendors, kafka, avro, source-table, upsert]
scenarios:
  - trace vendor kafka
  - debug vendor source write
  - follow vendor v2 batch
  - see vendor ack flow
  - inspect consumer concurrency
capabilities: [runtime-flow]
domains: [vendors]
entities: [VendorDataConsumer, VendorServiceImpl, VendorV2DataConsumer, VendorV2ServiceImpl]
sources:
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorDataConsumer.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorServiceImpl.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorV2DataConsumer.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorV2ServiceImpl.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/DedicatedKafkaConfig.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/VendorV2KafkaConfig.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - contracts/kafka-events.md
  - integrations/vendor-feeds.md
  - operations/failure-model.md
---

# Flow

1. `VendorDataConsumer` listens on `${kafka.vendor.data.topic}` with manual acknowledgements and hands each Avro `Vendor` record to `VendorService.saveVendorDataFeed`. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorDataConsumer.kt:18)
2. `VendorServiceImpl.saveVendorDataFeed` first writes the original payload into `vendors_source`, then calls `insertOrUpdate` to upsert the normalized `vendors` row. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorServiceImpl.kt:53)
3. `VendorV2DataConsumer` listens in batch mode on `${kafka.vendor.v2.data.topic}`, processes records with configurable concurrency, logs per-record failures, and swallows individual errors so the rest of the batch can continue. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorV2DataConsumer.kt:23)
4. `VendorV2ServiceImpl.saveVendorV2DataFeed` stores the raw event in `vendors_source_v2`, then upserts `vendors_v2`, preserving existing codes when the new message omits them. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorV2ServiceImpl.kt:26; vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorV2ServiceImpl.kt:52)
5. Both listener factories use manual-immediate ack mode, enable observation, and attach a fixed-backoff `DefaultErrorHandler`; V2 also enables batch listener mode. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/DedicatedKafkaConfig.kt:82; vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/VendorV2KafkaConfig.kt:108)
