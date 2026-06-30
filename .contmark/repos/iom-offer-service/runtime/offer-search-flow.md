---category: runtime
title: Offer Search Flow
summary: Step-by-step reactive flow for the main inland service plan search
primary_for: [offer-search-runtime-flow, service-plan-search-trace-guide]
mentions: [IOMSearchQueryController, IOMRoutingAndOfferService, RoutingService, OfferService, CustomsService, OfferedServicePlanPersistenceService, SearchRequestService]
scenarios:
  - trace search request end to end
  - understand how routes are fetched
  - understand offer api invocation
  - find where customs enrichment happens
  - understand parallelism in search flow
capabilities: [offer-search, routing, customs]
domains: [offer-management, routing, customs]
entities: [V3SearchRequest, OfferRequest, OfferedServicePlanDocument]
sources:
  - src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/RoutingService.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/OfferService.kt
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [runtime/rates-flow.md, runtime/service-plan-event-processing-flow.md, navigation/scenarios.md]
---

## Entry

`IOMSearchQueryController.getProposedServicePlans()` called from `searchServicePlans`, `searchServicePlansToAmend`, or `searchServicePlansStream` (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt:107)

## Phase 1 — Enrich & Persist Search Request

```
enrichSearchRequest(searchRequest)
  ├─ LocationService: resolve facility/city geoId details
  ├─ CommodityClient: validate commodity codes
  ├─ ContainerTypeClient: validate container types
  └─ CustomerClient: enrich party data (if needed)
persistSearchRequest(enrichedRequest.toDomain())  → MongoDB search_request
validateSearchRequestBasicValidator(enriched, context)
  └─ runs all IOMSearchValidator implementations in chain
zipWith(persisted) → attach searchRequestId to validated request
```
(source: src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt:112)

## Phase 2 — Routing

`RoutingService.getCombinedRoutes(searchRequest)` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RoutingService.kt:getCombinedRoutes):
1. Deduplicate booking equipments by ISO size type code
2. For each unique equipment type: build `V3RoutingRequestModel`, call `RoutingClientImpl.getRoutings()` in parallel on bounded-elastic scheduler
3. Group routes by `routingIdentifier`
4. Filter: keep only routes that serve ALL requested equipment types
5. Filter: transport mode must be in `allowedTransportModes`; code must not be in `disallowedTransportModesCode` (e.g., `FEF`)
6. Returns `V3RoutingResponseModel`

Routing condition: `PREFERRED` if `isFetchOnlyPreferredRouteEnabled` or `isScmBooking`; otherwise `ANY` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RoutingService.kt:buildRoutingRequest)

## Phase 3 — Offer API

`IOMRoutingAndOfferService.getFilteredOfferRequest()` assembles `OfferRequest` (routes + equipment + cargo weight validation) (source: src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt:getFilteredOfferRequest)

`OfferService.getOffers(offerRequest, triangulationEnabled, zonalPricingEnabled)`:
- Calls `OfferClientImpl.getOffers()` → OIPO v4 API
- Applies mandatory charges validation (feature-flagged) (source: src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt:applyMandatoryChargesValidation)

## Phase 4 — Parallelised Service Plan Preparation

`offerResponses.parallel().runOn(Schedulers.parallel())` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt:searchForServicePlans):
1. `prepareServicePlan(offerResponse, searchRequest, context)` — maps offer response to `V3ServicePlanModel`
2. **Customs enrichment** (if `isMyCustomsIntegrationEnabled`): `CustomsService.getCustomsChargesWithFallback()` → POST to customs API → merge charges into plan equipment (source: src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt:applyCustomsCharges)
3. `updatePreferredChargeName()` — resolves charge names from `ReferenceDataFetcher` cache
4. `saveProposedServicePlanDocument()` → INSERT into `offered_service_plans`

## Phase 5 — Response

Results are emitted as `ParallelFlux<V3ServicePlanModel>`, re-sequenced in controller and sorted by `zone` (nulls first) before returning to caller (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt:119)
