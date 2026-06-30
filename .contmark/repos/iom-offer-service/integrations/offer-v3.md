---
category: integrations
title: OIPO Offer v3/v4 Integration
summary: Integration with OIPO multicarrier offer API to retrieve inland transport offers
primary_for: [oipo-offer-integration-guide, offer-api-client-reference]
mentions: [OfferClientImpl, OfferClientWithMockFallback, OfferService, IOMRoutingAndOfferService, V3OfferRequestModel]
scenarios:
  - understand how offers are fetched
  - find offer api base url config
  - understand mock fallback on sit uat
  - trace offer client circuit breaker
  - add new field to offer request
capabilities: [offer-search]
domains: [offer-management]
entities: [OfferRequest, V3OfferRequestModel, MultiCarrierOfferResponseV3]
sources:
  - src/main/kotlin/com/maersk/iom/offer/webclient/offer/v3/OfferClient.kt
  - src/main/kotlin/com/maersk/iom/offer/webclient/offer/v3/V3OfferRequestModel.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [integrations/routing.md, runtime/offer-search-flow.md, operations/retries.md]
---

## System

OIPO (Ocean + Inland Product Offer) multicarrier offers API, version 4.

## Configuration

| Property | Value |
|---|---|
| Base URL | `${maersk.offers-base-url}retrieve-multicarrier-inland-offers` |
| API version header | `Api-version: ${services.routing.api-version:1}` |
| Auth | OAuth2 `client_credentials` via `offersAuthProvider` (Forgerock) |
| Circuit breaker | `offerClientInstance` |
| Retry count | 3, minBackOff 2s, jitter 0.75 |
(source: src/main/resources/application.yml:services.offers)

## Client: OfferClientImpl

`OfferClientImpl.getOffers(offerRequestModel, correlationId)`:
- POST to offers base URL with `Api-version` header
- On HTTP error: `ErrorHandler.handleExtendedExternalApiError()` — wraps in `ExtendedExternalApiException` (source: src/main/kotlin/com/maersk/iom/offer/webclient/offer/v3/OfferClient.kt:OfferClientImpl)
- Retry on `ExtendedExternalApiException` (except 422)
- Optional audit: if `features.offers.audit.enabled=true`, saves request+response to `OfferAudit` MongoDB collection (source: src/main/kotlin/com/maersk/iom/offer/webclient/offer/v3/OfferClient.kt:audit)

## Mock Fallback (SIT/UAT/Preprod)

`OfferClientWithMockFallback` (`@Primary`, `@Profile("sit","preprod","uat")`):
- Delegates to `OfferClientImpl` first
- On any error: falls back to `mockOffersClient` WebClient pointing to a mock service (source: src/main/kotlin/com/maersk/iom/offer/webclient/offer/v3/OfferClient.kt:OfferClientWithMockFallback)
- Logs `"Real offers API failed ... falling back to mock"`

## Request Shape

`V3OfferRequestModel` — contains routing data (origin, destination, legs), equipment list, cargo details, transport activity (source: src/main/kotlin/com/maersk/iom/offer/webclient/offer/v3/V3OfferRequestModel.kt)

## Response

`MultiCarrierOfferResponseV3` — list of offer products with charges, legs, service types, equipment-level pricing.
