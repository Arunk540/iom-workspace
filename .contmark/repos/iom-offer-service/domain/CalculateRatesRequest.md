---
category: domain
title: CalculateRatesRequest and RepriceRequest
summary: DTOs for rate calculation and repricing flows
primary_for: [rates-request-dto-reference, reprice-request-dto-reference]
mentions: [RatesController, RatesService, RatesClient, OfferServiceDetailsController]
scenarios:
  - understand calculate rates request fields
  - add new field to reprice request
  - find transport route structure for rates
  - understand additional charges in reprice
  - trace rates request construction
capabilities: [rate-calculation, reprice]
domains: [rates, offer-management]
entities: [CalculateRatesRequest, ContainerRateRequest, TransportRoute, TransportLeg, RepriceRequest, AdditionalChargeRequest]
sources:
  - src/main/kotlin/com/maersk/iom/offer/model/dto/rates/CalculateRatesRequest.kt
  - src/main/kotlin/com/maersk/iom/offer/model/dto/reprice/RepriceRequest.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [domain/OfferedServicePlanDocument.md, runtime/rates-flow.md, integrations/rates.md]
---

## CalculateRatesRequest

Top-level request sent to `POST /v3/rates/calculateRates` and forwarded to rates API.

| Field | Type | Constraint | Description |
|---|---|---|---|
| `offeredServicePlanNumber` | `String` | `@NotBlank` | Target offered plan |
| `containers` | `List<ContainerRateRequest>` | `@NotEmpty` | Per-container rate inputs |
| `customerCode` | `String` | `@NotBlank` | Booking party code |
| `transportRoutes` | `List<TransportRoute>` | `@NotEmpty` | Route legs with transport modes |
| `direction` | `String` | `@NotBlank` | `IMPORT` or `EXPORT` |
| `journeyType` | `String?` | — | e.g., `ONE_WAY_FULL`, `ROUND_TRIP` |
| `triangulation` | `String?` | — | Triangulation type name |

## ContainerRateRequest

| Field | Type | Description |
|---|---|---|
| `containerIds` | `List<String>` | Equipment identifiers to rate |
| `products` | `List<String>` | Product enum codes |
| `containerSizeType` | `String` | ISO size type code |
| `weight` / `weightUnit` | `BigDecimal` / `String` | Gross weight |
| `isNonOperatingReefer` | `Boolean?` | NOR flag |
| `isShipperOwnedContainer` | `Boolean?` | SOC flag |
| `isScm` | `Boolean?` | SCM booking |
| `optionalCharges` | `List<OptionalCharge>?` | Additional charge inputs |
| `shipmentPriceCalculationDate` | `String?` | `yyyy-MM-dd` format |

## TransportRoute / TransportLeg

Route structure mirrors the service plan legs. Built by `RatesService.buildTransportRoutes()` from `servicePlan.productOffer.products` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt:buildTransportRoutes)

Transport mode code mapping (TRANSPORT_MODE_CODE_MAP): `TRK/TRUCK→TRUCK`, `RR/RAIL→RAIL`, `BAR/BARGE→BARGE`, `BCO→BARGE_COMBINED`, `RCO→RAIL_COMBINED` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt:34)

## RepriceRequest

Sent to `POST /v3/service-plans-queries/{id}/repriced-offer`.

| Field | Type | Description |
|---|---|---|
| `containerDetails` | `List<ContainerDetails>` | Container execution status list |
| `priceCalculationDate` | `String?` | Override price date (`yyyy-MM-dd`) |
| `additionalCharges` | `List<AdditionalChargeRequest>?` | If present, routes to v8 rates API |
| `journeyType` | `JourneyType?` | One of `ONE_WAY_EMPTY`, `ONE_WAY_FULL`, `ROUND_TRIP`, etc. |

## AdditionalChargeRequest / AdditionalCharge

When `additionalCharges` is non-null, `RatesService.repriceWithAdditionalCharges()` is called instead of the standard re-offer flow (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt:170)

`AdditionalChargeType` enum: `WAITING_CHARGE`, `CHASSIS_RENTALS`, `STORAGE_TIME`, `FUEL_SURCHARGE` — mapped to material codes via `AdditionalChargeMaterialCodesProperties` (source: src/main/resources/application.yml:additional-charge-material-codes)
