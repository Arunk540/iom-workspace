---
category: integrations
title: Rates Integration
summary: Integration with OIPO rates v8 API for inland rate calculation and repricing
primary_for: [rates-api-integration-guide, rates-client-reference]
mentions: [RatesClientImpl, RatesService, RatesController, OfferServiceDetailsController]
scenarios:
  - understand rates api integration
  - find rates base url config
  - trace rates api request construction
  - understand rates circuit breaker
  - find rates retry configuration
capabilities: [rate-calculation, reprice]
domains: [rates]
entities: [CalculateRatesRequest, CalculateRatesResponse, CalculateRatesApiResponse]
sources:
  - src/main/kotlin/com/maersk/iom/offer/webclient/rates/RatesClient.kt
  - src/main/kotlin/com/maersk/iom/offer/model/dto/rates/CalculateRatesRequest.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [integrations/offer-v3.md, runtime/rates-flow.md, domain/CalculateRatesRequest.md]
---

## System

OIPO multicarrier rates API, version 8.

## Configuration

| Property | Value |
|---|---|
| Base URL | `${maersk.rates-base-url}retrieve-multicarrier-inland-offers` |
| Auth | OAuth2 `client_credentials` (provider TBD; `consumer-key` blank in dev) |
| Circuit breaker | `ratesClientInstance` |
| Retry count | 3, minBackOff 2s, jitter 0.75 |
(source: src/main/resources/application.yml:services.rates)

## Client: RatesClientImpl

`RatesClientImpl.calculateRates(request, correlationId)`:
- POST request body as JSON to rates base URL
- On HTTP error: `ErrorHandler.handleExtendedExternalApiError()` (source: src/main/kotlin/com/maersk/iom/offer/webclient/rates/RatesClient.kt:calculateRates)
- Returns `Flux<CalculateRatesResponse>` (one item per container group in response)
- Retry on `ExtendedExternalApiException` except 422

Fallback method `fallback()` wraps the exception in a new `ExtendedExternalApiException` with the original status code (source: src/main/kotlin/com/maersk/iom/offer/webclient/rates/RatesClient.kt:fallback)

## Stub (Dev/Test)

`RatesClientStub` — alternative implementation used in local/test profiles to return mock rate responses without calling external API (source: src/main/kotlin/com/maersk/iom/offer/webclient/rates/RatesClientStub.kt)

## Request → API Mapping

`CalculateRatesRequest` is sent directly as JSON to the rates API. The API returns a list of `CalculateRatesResponse` objects, each containing `containerLevelRates[].ratesPerContainer[]` with `chargeType`, `materialCode`, `amount`. (source: src/main/kotlin/com/maersk/iom/offer/model/dto/rates/CalculateRatesResponse.kt)

## Mandatory Charges

After receiving rates response, `RatesService.validateMandatoryCharges()` checks that all `chargeType=MANDATORY` material codes in the response are present in the configured mandatory charges list. A mismatch results in a `ValidationException` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt:validateMandatoryCharges)
