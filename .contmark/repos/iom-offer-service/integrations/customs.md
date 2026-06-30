---
category: integrations
title: Customs Integration
summary: Integration with customs services API for customs charge enrichment
primary_for: [customs-api-integration-guide, customs-charge-enrichment-reference]
mentions: [CustomsClientImpl, CustomsService, IOMRoutingAndOfferService, FeatureConfigUtil]
scenarios:
  - understand customs charge enrichment
  - find customs api base url
  - trace customs charge into service plan
  - understand customs feature flag
  - find customs export vs import logic
capabilities: [offer-search, customs]
domains: [offer-management, customs]
entities: [V3SearchRequest, V3ServicePlanModel, V3ChargeModel]
sources:
  - src/main/kotlin/com/maersk/iom/offer/webclient/customs/CustomsClient.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/CustomsService.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [integrations/business-rules.md, runtime/offer-search-flow.md, architecture/cross-cutting.md]
---

## System

Maersk customs services API.

## Configuration

| Property | Value |
|---|---|
| Base URL | `${maersk.base-url}customs-services/customs-rules` |
| Path suffix | `/product-offers-two` |
| Consumer key | `SSMsIObWRrp9Gi8KMyn3jEvvVcQZezSC` |
| Circuit breaker | `customsClientInstance` |
| Auth | OAuth2 `client_credentials` via `customsAuthProvider` (separate Forgerock client) |
(source: src/main/resources/application.yml:services.customs)

## Client: CustomsClientImpl

`CustomsClientImpl.getCustomsCharges(request)`:
- POST to `/product-offers-two`
- Returns `Mono<List<ProductOffer>>`
- Circuit breaker: `customsClientInstance` (source: src/main/kotlin/com/maersk/iom/offer/webclient/customs/CustomsClient.kt:getCustomsCharges)

## Service: CustomsService

`CustomsService.getCustomsChargesWithFallback(searchRequest)`:
1. Checks `hasCustoms=true` flag on booking equipment (source: src/main/kotlin/com/maersk/iom/offer/service/v3/CustomsService.kt:getCustomsRequest)
2. Groups containers by cargo weight, commodity code, and equipment size/type
3. Builds `CustomsRequest` with parties, export/import service mode, country and UN/LOCODE codes
4. Calls `CustomsClientImpl.getCustomsCharges()`
5. For EXPORT: looks for `exportCustomsClearance` product group with `Standard Export Declaration` product; charge type code `100247`
6. For IMPORT: looks for `importCustomsClearance` product group with `Standard Import Declaration` product; charge type code `100248`
7. On any error: falls back to empty map; logs warning (source: src/main/kotlin/com/maersk/iom/offer/service/v3/CustomsService.kt:getCustomsChargesWithFallback)

## Integration Point

`IOMRoutingAndOfferService.searchForServicePlans()` checks `isMyCustomsIntegrationEnabled()` LaunchDarkly flag before calling `CustomsService`. Customs charges are merged into `bookingEquipments[].charges` and `hasCustoms` flag set on each equipment (source: src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt:searchForServicePlans)
