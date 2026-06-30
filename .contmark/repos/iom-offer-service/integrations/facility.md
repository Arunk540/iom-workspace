---
category: integrations
title: Facility Integration
summary: Integration with SMDS facilities service for facility data lookup
primary_for: [facility-api-integration-guide, facility-client-reference]
mentions: [FacilityClient, OfferLocationService]
scenarios:
  - understand facility data lookup
  - find facility api base url
  - trace facility enrichment in search
  - find facility circuit breaker
  - understand facility vs city location
capabilities: [offer-search]
domains: [offer-management, routing]
entities: [SearchRequest, V3SearchRequest]
sources:
  - src/main/kotlin/com/maersk/iom/offer/webclient/facility/FacilityClient.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [integrations/location.md, runtime/offer-search-flow.md]
---

## System

SMDS (Shared Master Data Service) facilities service.

## Configuration

| Property | Value |
|---|---|
| Base URL | `${maersk.facility-base-url}facilities` (default: `https://smds-facilities-service.dev.maersk-digital.net/facilities`) |
| Circuit breaker | `facilityClientInstance` |
| Retry count | 3, minBackOff 2s, jitter 0.75 |
(source: src/main/resources/application.yml:maersk.facility-base-url)

## Client: FacilityClientImpl

`FacilityClientImpl` fetches facility details by facility code or ID. Used in `enrichSearchRequest()` to resolve facility metadata (facility code, postal address, UN/LOCODE) when origin or destination is specified as a facility (source: src/main/kotlin/com/maersk/iom/offer/webclient/facility/FacilityClient.kt)

Circuit breaker: `facilityClientInstance`; retry via `ErrorHandler.configureRetry` (source: src/main/kotlin/com/maersk/iom/offer/webclient/facility/FacilityClient.kt)

## Usage

When a search request specifies origin/destination as a facility (rather than a city), `OfferLocationService` calls `FacilityClient` to populate `V3FacilityModel` with:
- `facilityCode` — used as start/end location in routing request
- `postalAddress` with `iso2CountryCode` and `unLocCityCode` — used for customs request construction (source: src/main/kotlin/com/maersk/iom/offer/service/v3/CustomsService.kt:getCustomsRequest)
- `dataObject` — facility identifier passed to routing API (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RoutingService.kt:buildLocation)
