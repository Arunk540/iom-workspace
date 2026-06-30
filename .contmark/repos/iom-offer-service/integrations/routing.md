---
category: integrations
title: Routing Integration
summary: Integration with the delivery-network routing API for inland route discovery
primary_for: [routing-api-integration-guide, routing-client-reference]
mentions: [RoutingClientImpl, RoutingService, IOMRoutingAndOfferService, V3RoutingRequestModel]
scenarios:
  - understand how routes are fetched
  - find routing api base url
  - trace routing request construction
  - understand route filtering logic
  - find routing circuit breaker name
capabilities: [offer-search, routing]
domains: [routing, offer-management]
entities: [OfferRequest, V3RoutingModel, V3RoutingRequestModel, V3RoutingResponseModel]
sources:
  - src/main/kotlin/com/maersk/iom/offer/webclient/routing/v3/RoutingClient.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/RoutingService.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [integrations/offer-v3.md, runtime/offer-search-flow.md, operations/retries.md]
---

## System

Maersk delivery-network routing API.

## Configuration

| Property | Value |
|---|---|
| Base URL | `${maersk.routing-baseUrl}delivery-network/routing/routings-queries` |
| Consumer key | `${services.routing.consumer-key}` = `XWA3AiaKRxgAqCkDMKSo3S5AmSGCaUAz` |
| API version header | `Api-version: ${services.routing.api-version:1}` |
| Page | `${services.routing.page:1}` |
| Limit | `${services.routing.limit:10}` |
| Circuit breaker | `routingClientInstance` |
| Auth | OAuth2 `client_credentials` via `ireAuthProvider` |
(source: src/main/resources/application.yml:services.routing)

## Client: RoutingClientImpl

`RoutingClientImpl.getRoutings(routingRequestModel)`:
- POST with `Api-version` and `correlation-id` headers; query params `page` and `limit`
- Logs full routing response at INFO level (source: src/main/kotlin/com/maersk/iom/offer/webclient/routing/v3/RoutingClient.kt:getRoutings)
- Returns `Mono<V3RoutingResponseModel>`
- Retry on `ExtendedExternalApiException` except 422

## Request Construction

`RoutingService.buildRoutingRequest(equipment, searchRequest)`:
- `requestType = INTERMODAL`, `brandCode = MSL`
- Routing condition: `PREFERRED` if LaunchDarkly flag `isFetchOnlyPreferredRouteEnabled` is true or `isScmBooking`, else `ANY` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RoutingService.kt:buildRoutingRequest)
- Location built from facility `dataObject` if present, else city `dataObject`

## Route Filtering

After collecting all routes per equipment type:
1. Discard routes not serving all requested equipment types (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RoutingService.kt:discardRoutesNotServingForAllTheContainers)
2. Filter transport modes: must be in `allowedTransportModes` (`TRUCK,RAIL,BARGE,BARGE_COMBINED,RAIL_COMBINED`)
3. Filter transport mode codes: must not be in `disallowedTransportModesCode` (default `FEF`)
(source: src/main/kotlin/com/maersk/iom/offer/service/v3/RoutingService.kt:allowedTransportModes)

## Parallelism

Each equipment type results in a separate routing API call. Calls are parallelised using `ParallelFlux.runOn(Schedulers.boundedElastic())` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RoutingService.kt:getCombinedRoutes)
