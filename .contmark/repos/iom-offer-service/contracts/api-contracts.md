---category: contracts
title: API Contracts
summary: All REST API endpoints exposed by iom-offer-service
primary_for: [rest-api-contract-reference, openapi-endpoint-catalog]
mentions: [IOMSearchQueryController, RatesController, OfferServiceDetailsController, OfferServiceCleanUpController, ErrorCodeController]
scenarios:
  - find all exposed api endpoints
  - check request and response types
  - understand auth requirements per endpoint
  - find reprice endpoint signature
  - check error response codes
  - find the api contracts
  - check contract definitions
capabilities: [offer-search, rate-calculation, reprice, offer-cleanup, offer-details]
domains: [offer-management, rates]
entities: [V3SearchRequest, V3ServicePlanModel, CalculateRatesRequest, CalculateRatesApiResponse, RepriceRequest]
sources:
  - src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt
  - src/main/kotlin/com/maersk/iom/offer/controller/v3/RatesController.kt
  - src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt
  - src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceCleanUpController.kt
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [contracts/kafka-events.md, contracts/db-schemas.md, navigation/entry-points.md]
---

## Offer Search Endpoints

### POST /v3/service-plans-queries
- **Auth**: Role `Booking` or `SCMBooking` (checked per `isScmBooking` flag)
- **Request**: `V3SearchRequest` (JSON); `@JsonView(Views.NewOfferSearchRequest)` validation
- **Response**: `Flux<V3ServicePlanModel>` (JSON array)
- **Status codes**: 200, 204, 500, 501, 502
(source: src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt:46)

### PUT /v3/service-plans-queries
- **Auth**: Role `Booking` or `SCMBooking`
- **Request**: `V3SearchRequest` with `servicePlanNumber` populated; `@JsonView(Views.AmendSearchRequest)` validation
- **Response**: `Flux<V3ServicePlanModel>`
- **Status codes**: 200, 204, 500, 501
(source: src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt:70)

### POST /v3/service-plans-queries/stream
- **Auth**: Role `Booking` or `SCMBooking`
- **Request**: `V3SearchRequest` (JSON)
- **Response**: `Flux<V3ServicePlanModel>` (`text/event-stream`)
- **Status codes**: 200, 204, 500, 501
(source: src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt:92)

## Rates Endpoint

### POST /v3/rates/calculateRates
- **Auth**: Role `Booking` or `SCMBooking`
- **Request**: `CalculateRatesRequest` (JSON)
- **Response**: `Mono<CalculateRatesApiResponse>`
- **Status codes**: 200, 400, 401, 403, 500, 502
(source: src/main/kotlin/com/maersk/iom/offer/controller/v3/RatesController.kt:46)

## Offer Details & Reprice Endpoints

### GET /v3/offered-service-plan/{offeredServicePLanNumber}
- **Auth**: Role `permitted-role` (`booking.write,finance`)
- **Response**: `Mono<V3ServicePlanModel>`
- **Status codes**: 200, 404, 500
(source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt:61)

### POST /v3/offered-service-plan/{offeredServicePlanNumber}/equipments/delete
- **Auth**: Role `Booking` or `SCMBooking`
- **Request**: `V3DeleteEquipmentsRequest`
- **Response**: `Mono<V3ServicePlanModel>`
(source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt:86)

### GET /v3/search-request/{searchRequestId}
- **Auth**: `permitAll()`
- **Response**: `Mono<V3SearchRequest>`
(source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt:108)

### GET /v3/service-plans-queries/{id}/repriced-offer *(deprecated)*
- **Auth**: `permitAll()`
- **Response**: `Mono<V3ServicePlanModel>`
(source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt:123)

### POST /v3/service-plans-queries/{id}/repriced-offer
- **Auth**: `permitAll()`
- **Request**: `RepriceRequest` (JSON)
- **Response**: `Mono<V3ServicePlanModel>`
(source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt:151)

## Cleanup Endpoint

### DELETE /v3/offered-service-plan?numberOfDays=N
- **Auth**: `permitAll()`
- **Response**: `Mono<OfferRecordsDeleteResponse>`
- **Status codes**: 200, 204, 500, 501, 502
(source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceCleanUpController.kt:34)

## Error Codes Endpoint

### GET /v3/error-codes
- **Auth**: none required
- **Response**: static error code catalog
(source: src/main/kotlin/com/maersk/iom/offer/controller/v3/ErrorCodeController.kt)
