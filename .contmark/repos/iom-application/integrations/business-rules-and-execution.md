---category: integrations
title: Business rules, Telikos, and notifications
summary: Captures the outbound integrations used for appointment-day and commodity-restriction rules, Telikos execution flows, and notification delivery.
primary_for: [execution-integration-map]
mentions: [business rules client, telikos, notification webhook, appointment days]
scenarios:
  - trace telikos flow
  - find business rules api
  - how notifications sent
  - where execution calls live
  - commodity restriction endpoint
  - trace rule execution
capabilities: [integration-mapping]
domains: [reference-data]
entities: [BusinessRulesClient, TelikosClient, NotificationClient]
sources:
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/config/WebClientConfiguration.kt
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/businessRules/BusinessRulesClient.kt
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/telikos/TelikosClient.kt
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/notifications/NotificationClient.kt
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/ErrorHandler.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - operations/failure-model.md
  - runtime/order-processing.md
---
## Business rules

- `WebClientConfiguration.businessRulesClient()` creates an OAuth2 + consumer-key client for the external business-rules base URL. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/config/WebClientConfiguration.kt:280)
- `BusinessRulesClientImpl` POSTs `BusinessRuleRequest` payloads to `/appointment-days/appointment-days-rules` and `/commodity-restrictions/commodity-restrictions`, then retries them with error-code-aware retry handling. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/businessRules/BusinessRulesClient.kt:25)

## Telikos execution

- `WebClientConfiguration.telikosClient()` creates an OAuth2 + consumer-key client for `services.telikos.base-url`. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/config/WebClientConfiguration.kt:291)
- `TelikosClientImpl` owns four execution-facing calls: `sendToExecution()` to `/{booking}/execution-instructions`, `sendToTms()` to `/{booking}/send-to-tms`, `confirmBooking()` to `/{booking}/status`, and `getConfig()` to `/{booking}/configs`. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/telikos/TelikosClient.kt:26)

## Notifications and shared error policy

- `NotificationClientImpl.sendNotification()` POSTs webhook payloads to `/api/v1/webhook/receive/notification` and reports success/failure against booking and user IDs. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/notifications/NotificationClient.kt:18)
- The shared `ErrorHandler` converts 5xx responses into `ExternalApiException` and configures exponential backoff retries only for those external-api failures. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/ErrorHandler.kt:22)
