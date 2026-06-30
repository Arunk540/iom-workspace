---
category: runtime
title: Rates Flow
summary: Rate calculation and repricing flows for an offered service plan
primary_for: [rates-calculation-runtime-flow, reprice-runtime-flow]
mentions: [RatesController, RatesService, RatesClientImpl, OfferedServicePlanPersistenceService, OfferServiceDetailsController]
scenarios:
  - calculate rates for offered plan
  - reprice offer with price date change
  - reprice with additional charges
  - understand mandatory charge validation
  - trace rates merge into service plan
capabilities: [rate-calculation, reprice, persistence]
domains: [rates, offer-management]
entities: [CalculateRatesRequest, CalculateRatesApiResponse, RepriceRequest, OfferedServicePlanDocument]
sources:
  - src/main/kotlin/com/maersk/iom/offer/controller/v3/RatesController.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt
  - src/main/kotlin/com/maersk/iom/offer/webclient/rates/RatesClient.kt
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [runtime/offer-search-flow.md, domain/CalculateRatesRequest.md, integrations/rates.md]
---

## Flow A — Calculate Rates

**Entry**: `POST /v3/rates/calculateRates` (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/RatesController.kt:46)

### Steps

1. **Fetch offered plan** — `OfferedServicePlanPersistenceService.findByOfferedServicePlanNumber(request.offeredServicePlanNumber)` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt:calculateRates)
2. **Validate container IDs** — confirm all `request.containers[].containerIds` exist in the plan's `productOffer.products[].bookingEquipments` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt:validateContainerIds)
3. **Call rates API** — `RatesClientImpl.calculateRates(request, correlationId)` → POST to rates v8 endpoint; returns `Flux<CalculateRatesResponse>`, collected to list (source: src/main/kotlin/com/maersk/iom/offer/webclient/rates/RatesClient.kt:calculateRates)
4. **Validate mandatory charges** — `validateMandatoryCharges()`: for each transport leg, look up configured mandatory charge codes by direction+transport mode; fail if API returns a `MANDATORY` charge code not in the configured list (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt:validateMandatoryCharges)
5. **Merge & persist** — `mergeChargesIntoServicePlan()` merges new charges into existing `productOffer`; `persistenceService.updateProductOffer()` writes back to MongoDB (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt:mergeAndPersist)
6. **Return** `CalculateRatesApiResponse` to caller

### Mandatory Charge Validation

Transport mode code map: `TRK/TRUCK→TRUCK`, `RR/RAIL→RAIL`, `BAR/BARGE→BARGE` etc. (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt:34)
Configured codes loaded from `MandatoryChargesProperties` (IMPORT/EXPORT × truck/rail/barge) (source: src/main/resources/application.yml:mandatory-charges)

## Flow B — Reprice (No Additional Charges)

**Entry**: `POST /v3/service-plans-queries/{id}/repriced-offer` with empty `additionalCharges` (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt:151)

1. Fetch offered plan + linked search request
2. Optionally update `timeRange.from` with `priceCalculationDate`
3. Re-persist search request
4. `IOMRoutingAndOfferService.getOfferResponse()` — re-runs offer lookup using existing routing data
5. Save updated offered service plan document

## Flow C — Reprice with Additional Charges

**Entry**: `POST /v3/service-plans-queries/{id}/repriced-offer` with non-empty `additionalCharges` (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt:170)

1. `RatesService.repriceWithAdditionalCharges(offeredServicePlanNumber, additionalCharges, journeyType)` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt:repriceWithAdditionalCharges)
2. Build `CalculateRatesRequest` via `buildCalculateRatesRequest()` — maps `AdditionalChargeRequest` items to `OptionalCharge` with material codes from `AdditionalChargeMaterialCodesProperties` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt:buildCalculateRatesRequest)
3. Call rates v8 API, validate mandatory charges, merge charges, persist as `V3ServicePlanModel`
