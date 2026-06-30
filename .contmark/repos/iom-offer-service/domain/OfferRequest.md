---
category: domain
title: OfferRequest
summary: Internal aggregation of routing results and booking equipments passed to the offer API
primary_for: [offer-request-domain-model, routing-to-offer-handoff-contract]
mentions: [IOMRoutingAndOfferService, OfferService, OfferClientImpl, V3OfferRequestModel]
scenarios:
  - understand offer api input structure
  - trace routing output to offer input
  - find triangulation type usage
  - understand correlation id propagation
  - add new field to offer request
capabilities: [offer-search, routing]
domains: [offer-management, routing]
entities: [OfferRequest, OfferResponse, V3RoutingModel, V3BookingEquipmentModel]
sources:
  - src/main/kotlin/com/maersk/iom/offer/domain/OfferRequest.kt
  - src/main/kotlin/com/maersk/iom/offer/domain/OfferResponse.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [domain/SearchRequest.md, domain/OfferedServicePlanDocument.md, runtime/offer-search-flow.md]
---

## Overview

`OfferRequest` is the internal assembly object created after routing is complete and before calling the OIPO offer API. It is annotated `@InternalOfferClass` to signal it must not leak to external contracts.

## Data Class Fields

| Field | Type | Description |
|---|---|---|
| `correlationId` | `String` | UUID linking routing call to offer call; used in logs |
| `searchRequest` | `V3SearchRequest` | Original enriched search request |
| `routingModels` | `List<V3RoutingModel>` | Routes returned by routing API after filtering |
| `bookingEquipments` | `List<V3BookingEquipmentModel>` | Enriched equipment models (with location/commodity data) |
| `triangulation` | `TriangulationTypeEnum?` | Optional; `NONE`, `ORIGIN`, or `DESTINATION` — controls offer type selection |

## Construction

`getFilteredOfferRequest()` in `IOMRoutingAndOfferService` assembles `OfferRequest`:
1. `routingServiceV3.getCombinedRoutes(searchRequest)` — produces `List<V3RoutingModel>` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt:getFilteredOfferRequest)
2. `cargoWeightClient.getCargoWeight()` — validates weight constraints
3. Combines with booking equipments from `searchRequest`
4. `TriangulationTypeEnum` sourced from `featureConfigUtil.isTriangulationEnabled(country)` result

## Usage

`OfferService.getOffers(offerRequest, triangulationEnabled, zonalPricingEnabled)` maps `OfferRequest` to `V3OfferRequestModel` and calls the OIPO offer API. Returns a `Flux<OfferResponse>` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/OfferService.kt:getOffers)

## OfferResponse

`OfferResponse` wraps the raw API response alongside the `searchRequest` for use in `prepareServicePlan()`. Not a MongoDB document — only in-memory during the reactive pipeline (source: src/main/kotlin/com/maersk/iom/offer/domain/OfferResponse.kt)
