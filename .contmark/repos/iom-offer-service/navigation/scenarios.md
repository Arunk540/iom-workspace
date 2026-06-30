---category: navigation
title: User Scenarios
summary: End-to-end user scenarios supported by iom-offer-service
primary_for: [scenario-index, use-case-navigation-map]
mentions: [IOMSearchQueryController, RatesController, OfferServiceDetailsController, OfferServiceCleanUpController, ServicePlanDomainEventConsumer]
scenarios:
  - search service plans for booking
  - amend existing service plan search
  - calculate inland transport rates
  - reprice offer with new date
  - clean up unmapped offer records
  - get offered service plan details
  - reprice with additional charges
  - find the right scenario
  - match a user scenario
  - find the right use case
  - match a use case
capabilities: [offer-search, rate-calculation, reprice, offer-cleanup, offer-details]
domains: [offer-management, rates, routing, customs]
entities: [V3SearchRequest, OfferedServicePlanDocument, CalculateRatesRequest, RepriceRequest]
sources:
  - src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt
  - src/main/kotlin/com/maersk/iom/offer/controller/v3/RatesController.kt
  - src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt
  - src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceCleanUpController.kt
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [navigation/entry-points.md, runtime/offer-search-flow.md, runtime/rates-flow.md]
---

## Scenario Map

### 1. Search Inland Service Plans
**Trigger**: Booking agent or SCM user initiates a new shipment search.  
**Entry**: `POST /v3/service-plans-queries`  
**Flow**: enrich → persist search request → validate → route → offer → customs → persist offered plans  
**Result**: Flux of `V3ServicePlanModel` sorted by zone

### 2. Amend Service Plan Search
**Trigger**: Amend workflow references an existing `servicePlanNumber`.  
**Entry**: `PUT /v3/service-plans-queries`  
**Flow**: Same pipeline as new search but `servicePlanNumber` populated in request; sorted Flux result  
**Key difference**: `@JsonView(Views.AmendSearchRequest)` validation group applied (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt:75)

### 3. Calculate Rates
**Trigger**: User selects a service plan and requests rate pricing.  
**Entry**: `POST /v3/rates/calculateRates`  
**Flow**: find plan → validate container IDs → call rates API → validate mandatory charges → merge & persist  
**Result**: `CalculateRatesApiResponse` with per-container rate breakdown

### 4. Reprice Offer
**Trigger**: Re-price previously offered plan (e.g., date change).  
**Entry**: `GET|POST /v3/service-plans-queries/{id}/repriced-offer`  
**Flow (no additional charges)**: fetch plan + search request → rebuild offer → save  
**Flow (with additional charges)**: `RatesService.repriceWithAdditionalCharges` → v8 pricing API (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt:170)

### 5. Clean Up Unmapped Offers
**Trigger**: Scheduled maintenance call.  
**Entry**: `DELETE /v3/offered-service-plan?numberOfDays=N`  
**Flow**: query `offered_service_plans` where `servicePlanNumber IS NULL AND createdTime < cutoff` → delete  
**Result**: `OfferRecordsDeleteResponse` with count deleted (source: src/main/kotlin/com/maersk/iom/offer/service/v3/OfferRecordCleanUpService.kt:removeUnmappedOffers)

### 6. Link Offered Plan to Booked Service Plan
**Trigger**: Booking confirms and emits `ServicePlanDomainEvent` on Kafka.  
**Consumer**: `ServicePlanDomainEventConsumer` calls `updateServicePlanNumber` to set `servicePlanNumber` on the document (source: src/main/kotlin/com/maersk/iom/offer/messaging/ServicePlanDomainEventConsumer.kt:36)

### 7. Get Offer Details
**Trigger**: UI or downstream service needs full plan object.  
**Entry**: `GET /v3/offered-service-plan/{id}`  
**Flow**: `findByOfferedServicePlanNumber` → map to `V3ServicePlanModel` (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt:68)
