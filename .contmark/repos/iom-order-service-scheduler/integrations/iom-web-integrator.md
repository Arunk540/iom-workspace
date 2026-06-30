---
category: integrations
title: IOM Web Integrator
summary: Repricing activities call the Web Integrator API through an OAuth-protected WebClient. The integration is PATCH-based and returns a `ServicePlan` body.
primary_for: [web-integrator-repricing]
mentions: [repricing-workflow-execution, azure-oauth-client, external-http-timeouts]
scenarios:
  - repricing api failing
  - web integrator patch
  - repricing http call
  - where repricing api lives
  - service plan reprice endpoint
capabilities: [repricing]
domains: [IOM]
entities: [ServicePlan, RepriceRequest]
peer_systems: [iom-web-integrator]
direction: outbound
protocol: rest
topic_or_endpoint: PATCH /service-plans-queries/service-plans/{servicePlanNumber}/reprice
sources:
  - src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt
  - src/main/kotlin/com/maersk/iom/offer/WebIntegratorClient.kt
  - src/main/kotlin/com/maersk/iom/offer/WebClientConfiguration.kt
  - src/main/resources/application.yaml
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - runtime/repricing-flow.md
  - integrations/temporal.md
---

# IOM Web Integrator integration

- `WebClientConfiguration.iomWebIntegratorClient()` builds the client from `services.iom-wi.base-url` and applies the `azureOauth2Client` filter (source: src/main/kotlin/com/maersk/iom/offer/WebClientConfiguration.kt:68; source: src/main/resources/application.yaml:57)
- `RepricingActivityImpl` is the only caller and delegates to `WebIntegratorClient.repriceBooking(...)` from Temporal activity code (source: src/main/kotlin/com/maersk/iom/workflows/repricing/RepricingWorkflow.kt:46)
- The HTTP method is `PATCH` on `/service-plans-queries/service-plans/{servicePlanNumber}/reprice` and the request body is `RepriceRequest(reason=OTHER, comment, priceCalculationDate?)` (source: src/main/kotlin/com/maersk/iom/offer/WebIntegratorClient.kt:27)
- Comment text switches between vessel ETA/ETD repricing and container-count repricing based on whether `priceCalculationDate` is supplied (source: src/main/kotlin/com/maersk/iom/offer/WebIntegratorClient.kt:29)
- Any 4xx or 5xx response body is wrapped into a `RuntimeException`, which bubbles back through the activity and workflow stack (source: src/main/kotlin/com/maersk/iom/offer/WebIntegratorClient.kt:44)
- Shared timeouts and `X-Requestor` header come from `webClientBuilder(...)` and the shared Reactor Netty client (source: src/main/kotlin/com/maersk/iom/offer/WebClientConfiguration.kt:91)
