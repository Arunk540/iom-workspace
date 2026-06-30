---
category: operations
title: Retry and backoff behavior
summary: Retries are centralized around outbound WebClient calls and vendor Kafka listener error handlers.
primary_for: [retry-behavior-map]
mentions: [retry, backoff, webclient, kafka, ack]
scenarios:
  - see retry policy
  - debug backoff
  - inspect kafka error handler
  - find ack mode
  - see outbound retry count
capabilities: [operations-guide]
domains: [platform, location, commodity, vendors]
entities: [ErrorHandler, CommodityRetryConfig, DedicatedKafkaConfig, VendorV2KafkaConfig]
sources:
  - service/src/main/resources/application.yaml
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/configuration/ErrorHandler.kt
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/webclient/configuration/ErrorHandler.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/DedicatedKafkaConfig.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/VendorV2KafkaConfig.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - runtime/location-search-flow.md
  - runtime/commodity-restrictions-flow.md
  - runtime/vendor-ingest-flow.md
---

# Retry model

- Shared outbound retry defaults are `count: 3`, `minBackOff: 2`, and `jitter: 0.75` under `services.external.retry`. (source: service/src/main/resources/application.yaml:298)
- Location and commodity `ErrorHandler` implementations only retry failures converted into `ExternalApiException`, which means 5xx responses are retried but client-side validation/4xx responses are not. (source: location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/configuration/ErrorHandler.kt:27; commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/webclient/configuration/ErrorHandler.kt:27)
- Vendor V1 and V2 listeners both use `DefaultErrorHandler(FixedBackOff(100, 3))` by default and require manual-immediate acknowledgements. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/DedicatedKafkaConfig.kt:66; vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/VendorV2KafkaConfig.kt:70)
- V2 adds consumer timeout tuning (`session-timeout-ms`, `heartbeat-interval-ms`, `max-poll-interval-ms`) to avoid rebalancing during batch processing. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/VendorV2KafkaConfig.kt:76)
