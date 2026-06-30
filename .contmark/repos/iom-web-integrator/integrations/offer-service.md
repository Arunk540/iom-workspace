---
category: integrations
title: Offer service integration
summary: Offer-service provides new-booking search, amendment search, repriced offers, offered-service-plan retrieval, and equipment deletion.
primary_for: [offer-service-integration]
mentions: [offer service, repricing backend, offered plan, search quotes]
scenarios:
  - offer service calls
  - repricing backend
  - offer api integration
  - offered service plan api
  - quote search backend
capabilities: [integration-offer-service]
domains: [Offers]
entities: [V3SearchRequest, V3ServicePlanModel]
sources:
  - src/main/resources/application.yml
  - src/main/kotlin/com/maersk/iom/webintegrator/config/WebClientConfiguration.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/OfferClient.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - runtime/offer-search-flow.md
  - runtime/repricing-flow.md
---
# Offer service integration

- Base URL is `services.offer.base-url`, defaulting to `https://iom-offer-service.dev.maersk-digital.net`. (source: src/main/resources/application.yml:45)
- `offerClient` WebClient is registered against that base URL. (source: src/main/kotlin/com/maersk/iom/webintegrator/config/WebClientConfiguration.kt:107)
- New-booking search posts to `/v3/service-plans-queries`; amend search puts to the same path. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/OfferClient.kt:40)
- Repricing posts to `/v3/service-plans-queries/{offeredServicePlanNumber}/repriced-offer`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/OfferClient.kt:92)
- Equipment deletion posts to `/v3/offered-service-plan/{offeredServicePlanNumber}/equipments/delete`, and offered-plan lookup reads `/v3/offered-service-plan/{offeredServicePlanNumber}`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/OfferClient.kt:120)
- HTTP 204 and 206 are treated as domain exceptions during search so upstream flows can distinguish “no route” from “partial data.” (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/OfferClient.kt:49)
