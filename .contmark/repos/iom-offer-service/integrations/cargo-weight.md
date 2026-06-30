---
category: integrations
title: Cargo Weight Integration
summary: Integration with cargo weight rules API for container weight validation
primary_for: [cargo-weight-api-integration-guide, weight-validation-reference]
mentions: [CargoWeightClientImpl, CargoWeightValidationService, IOMRoutingAndOfferService]
scenarios:
  - understand cargo weight validation
  - find cargo weight api base url
  - trace weight check in search flow
  - find cargo weight circuit breaker
  - understand weight restriction countries
capabilities: [offer-search, validation]
domains: [offer-management]
entities: [SearchRequest, ContainerRequest]
sources:
  - src/main/kotlin/com/maersk/iom/offer/webclient/cargo/CargoWeightClient.kt
  - src/main/kotlin/com/maersk/iom/offer/service/CargoWeightValidationService.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [integrations/business-rules.md, runtime/offer-search-flow.md]
---

## System

Maersk container-and-cargo-weight rules API (under `dh` business rules domain).

## Configuration

| Property | Value |
|---|---|
| Base URL | `${maersk.baseUrl}/dh/container-and-cargo-weight` |
| Path | `/container-and-cargo-weight-rules` |
| Consumer key | `${iomBusinessRulesConsumerKey}` |
| Circuit breaker | `cargoWeightClientInstance` |
| Retry count | 3, minBackOff 2s, jitter 0.75 |
(source: src/main/resources/application.yml:services.cargo-weightType)

## Client: CargoWeightClientImpl

`CargoWeightClientImpl.getCargoWeight(cargoWeight)`:
- POST `CargoWeightRequest` to `/container-and-cargo-weight-rules`
- Returns `Mono<CargoWeightResponse>` with min/max weight limits per container type (source: src/main/kotlin/com/maersk/iom/offer/webclient/cargo/CargoWeightClient.kt:getCargoWeight)
- On error: throws the error and logs; circuit breaker on `cargoWeightClientInstance`

## Weight Validation

`CargoWeightValidationService` uses the response to validate that `expectedNetWeight` per equipment is within the allowed range for the container type. Called in `IOMRoutingAndOfferService.getFilteredOfferRequest()` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt:getFilteredOfferRequest)

Country weight restriction feature flag: `features.offers.countryweightrestrictions.enabled=true` enables per-country weight restriction checks (source: src/main/resources/application.yml:features.offers.countryweightrestrictions)

Weight is converted from LBS to KGS using `KGS_TO_LBS` constant in `OfferConstants` when `weightUnit` is LBS (source: src/main/kotlin/com/maersk/iom/offer/webclient/offer/OfferConstants.kt:KGS_TO_LBS)
