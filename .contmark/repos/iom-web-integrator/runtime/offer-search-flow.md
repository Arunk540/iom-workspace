---
category: runtime
title: Offer search flow
summary: Offer search checks pricing-country flags, maps the search request to downstream V3 models, and optionally merges ocean-booking metadata into the returned service plans.
primary_for: [offer-search-flow]
mentions: [offer search, route search, search service plans, amend search]
scenarios:
  - offer search flow
  - service plan search
  - amend offer flow
  - route quote trace
  - offer search debug
capabilities: [offer-search]
domains: [Offers]
entities: [SearchRequest, ServicePlan]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/OfferService.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/mapper/V3SearchRequestMapper.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/OfferClient.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - runtime/ocean-booking-enrichment-flow.md
  - integrations/offer-service.md
---
# Offer search flow

- `OfferController.searchServicePlans(...)` logs the inbound `SearchRequest`, optionally updates container counts for special price owners, filters invalid dangerous-goods measurements, and decides whether the search is a duplicate flow. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt:77)
- `OfferService.searchOffer(...)` first checks `isPricingCountry` and `isLocalOceanChargesEnabled`; non-pricing countries return a synthetic fallback service plan instead of calling offer service. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OfferService.kt:40)
- Downstream mapping uses `V3SearchRequestMapper.toModel(...)` to translate BFF search structures into offer-service `V3SearchRequest` payloads. (source: src/main/kotlin/com/maersk/iom/webintegrator/mapper/V3SearchRequestMapper.kt:46)
- `OfferClient.searchOffer(...)` posts to `/v3/service-plans-queries`, mapping HTTP 204 to `NoAppropriateDataFoundException` and HTTP 206 to `PartialDataFoundException`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/OfferClient.kt:40)
- If an ocean reference number is present, `OfferService` additionally merges bill-of-lading summary, carrier, and party details into each returned service plan. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OfferService.kt:61)
- Amendment searches reuse the same pattern but compute a versioned request and validate SCM/non-SCM transition rules before calling the offer amend endpoint. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OfferService.kt:96)
