---
category: operations
title: Retry policies
summary: Retry behavior is explicit for Kafka listener containers and declarative for outbound service calls. Consumers use fixed backoff with manual acknowledgment, while YAML carries external-service retry knobs for shared clients.
primary_for: [retry-policy]
mentions: [kafka-retries, backoff, external-retry, ack-mode, auto-commit]
scenarios:
  - inspect kafka retry
  - inspect external retry
  - inspect ack policy
  - inspect backoff values
  - inspect consumer failure handling
capabilities: [operations]
domains: [runtime]
entities: [DefaultErrorHandler, FixedBackOff, Acknowledgment, ConsumerFactory]
sources:
  - src/main/resources/application.yaml
  - src/main/kotlin/com/maersk/iom/config/KafkaConfig.kt
  - src/main/kotlin/com/maersk/iom/messaging/BookingUpdatesEventHandler.kt
  - src/main/kotlin/com/maersk/iom/messaging/PricingUpdatesEventHandler.kt
  - src/main/kotlin/com/maersk/iom/messaging/CustomerBookingHistoryEventConsumer.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - contracts/kafka-events.md
  - operations/failure-model.md
  - operations/monitoring.md
---
- All Kafka listener factories install `DefaultErrorHandler(FixedBackOff(errorMaxBackoffInterval, errorMaxBackoffRetry))`; the defaults come from booking consumer config `100ms` and `3` retries. (source: src/main/kotlin/com/maersk/iom/config/KafkaConfig.kt:106-110, src/main/kotlin/com/maersk/iom/config/KafkaConfig.kt:271-297, src/main/resources/application.yaml:368-369, src/main/resources/application.yaml:420-421)
- All listener factories set `AckMode.MANUAL_IMMEDIATE` and disable `ENABLE_AUTO_COMMIT`. (source: src/main/kotlin/com/maersk/iom/config/KafkaConfig.kt:221-223, src/main/kotlin/com/maersk/iom/config/KafkaConfig.kt:251-253, src/main/kotlin/com/maersk/iom/config/KafkaConfig.kt:276-297)
- Generic outbound retry knobs live under `services.external.retry.count|minBackOff|jitter`, which shared clients/resolvers can consume. (source: src/main/resources/application.yaml:153-160)
- Booking-status and pricing handlers may acknowledge on errors when `kafka.booking.consumer.acknowledgementReq` is true; customer-history acknowledges after converting failures into fallback strings. (source: src/main/kotlin/com/maersk/iom/messaging/BookingUpdatesEventHandler.kt:53-61, src/main/kotlin/com/maersk/iom/messaging/PricingUpdatesEventHandler.kt:40-44, src/main/kotlin/com/maersk/iom/messaging/CustomerBookingHistoryEventConsumer.kt:66-79)
