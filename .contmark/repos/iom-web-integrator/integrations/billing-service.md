---
category: integrations
title: Billing service integration
summary: Billing-service supplies costs, invoice-trigger eligibility, invoice triggering, and soft-close/cost update side effects for bookings.
primary_for: [billing-service-integration]
mentions: [billing service, costs, invoice trigger, soft close]
scenarios:
  - billing service calls
  - invoice trigger backend
  - cost update backend
  - soft close backend
  - billing integration debug
capabilities: [integration-billing-service]
domains: [Billing]
entities: [FinancialJobLine, InvoiceTriggerRequest, InvoiceTriggerResponse]
sources:
  - src/main/resources/application.yml
  - src/main/kotlin/com/maersk/iom/webintegrator/config/WebClientConfiguration.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/BillingClient.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - runtime/order-lifecycle-flow.md
  - operations/retries.md
---
# Billing service integration

- Base URL is `services.billing.url`, defaulting to `https://telikos-billing-service.sit.maersk-digital.net/billing`, with consumer-key and API-Version headers added at bean creation. (source: src/main/resources/application.yml:62)
- The billing WebClient is registered with `API-Version` and consumer-key defaults in `WebClientConfiguration.billingClient(...)`. (source: src/main/kotlin/com/maersk/iom/webintegrator/config/WebClientConfiguration.kt:128)
- Cost retrieval reads `/{financialJobNumber}/financial-job-lines?type=cost`; cost updates put the same resource without the query param. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/BillingClient.kt:43)
- Soft close patches `/{financialJobNumber}/soft-close`, and invoice-trigger eligibility reads `/{financialJobNumber}/invoice/trigger-validation`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/BillingClient.kt:87)
- Invoice triggering posts to `/trigger-invoice`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/BillingClient.kt:127)
- `manageCostsAndDeleteDraftRevenues(...)` is protected by a Resilience4j circuit breaker named `billingClientInstance`; fallback returns `ExtendedExternalApiException`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/BillingClient.kt:60)
