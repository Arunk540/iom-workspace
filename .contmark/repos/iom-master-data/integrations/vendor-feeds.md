---
category: integrations
title: Vendor Kafka feeds and schema registry
summary: Vendor ingestion depends on versioned Kafka clusters, SASL/TLS settings, and Confluent schema registry integration.
primary_for: [vendor-feed-integration-map]
mentions: [kafka, schema-registry, vendor-v1, vendor-v2, sasl]
scenarios:
  - find vendor topic config
  - inspect schema registry
  - see vendor kafka auth
  - debug vendor cluster settings
  - find v2 concurrency config
capabilities: [integration-map]
domains: [vendors]
entities: [DedicatedKafkaConfig, VendorV2KafkaConfig]
sources:
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/DedicatedKafkaConfig.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/VendorV2KafkaConfig.kt
  - service/src/main/resources/application.yaml
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - contracts/kafka-events.md
  - runtime/vendor-ingest-flow.md
  - operations/failure-model.md
---

# Systems

- V1 feed settings live under `kafka.vendor.*`, including bootstrap servers, SASL, schema registry, truststores, and `${kafka.vendor.data.consumer.group}`. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/DedicatedKafkaConfig.kt:21; service/src/main/resources/application.yaml:152)
- V2 feed settings live under `kafka.vendor.v2.*`, with separate bootstrap servers, schema registry, and consumer tuning for concurrency, poll intervals, and retries. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/VendorV2KafkaConfig.kt:25; service/src/main/resources/application.yaml:180)
- Both integrations enable SASL/TLS properties only when the `saslRequired` flag is true and expect truststore passwords to be base64-decoded before injection into Kafka properties. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/DedicatedKafkaConfig.kt:92; vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/VendorV2KafkaConfig.kt:133)
