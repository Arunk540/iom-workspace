---category: integrations
title: Location Integration
summary: Integration with master data location API to resolve geoId-based location details
primary_for: [location-api-integration-guide, location-client-reference]
mentions: [LocationClient, LocationService, OfferLocationService]
scenarios:
  - understand how location data is fetched
  - find location api base url
  - trace location enrichment in search flow
  - find location client circuit breaker
  - understand geo id based location lookup
capabilities: [offer-search]
domains: [offer-management, routing]
entities: [SearchRequest, V3SearchRequest]
sources:
  - src/main/kotlin/com/maersk/iom/offer/webclient/location/LocationClient.kt
  - src/main/kotlin/com/maersk/iom/offer/service/LocationService.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/OfferLocationService.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [integrations/facility.md, runtime/offer-search-flow.md]
---

## System

IOM Master Data Service — location endpoint.

## Configuration

| Property | Value |
|---|---|
| Base URL | `${maersk.masterdata-base-url}locations/{geoId}` |
| Circuit breaker | `locationClientInstance` |
| Auth | inherited from masterdata WebClient |
(source: src/main/resources/application.yml:services.location)

## Client: LocationClientImpl

`LocationClientImpl` fetches location details by geoId. Used in `enrichSearchRequest()` to resolve origin/destination location metadata (facility codes, UN/LOCODE, country code) needed for routing requests (source: src/main/kotlin/com/maersk/iom/offer/webclient/location/LocationClient.kt)

Circuit breaker: `locationClientInstance`; retry via `ErrorHandler.configureRetry` (source: src/main/kotlin/com/maersk/iom/offer/webclient/location/LocationClient.kt)

## Usage in Search Flow

`OfferLocationService` uses `LocationClient` to:
1. Resolve origin and destination locations from geoId in `V3SearchRequest`
2. Populate `V3FacilityModel` or `V3CityModel` used in routing request construction
3. Determine ISO2 country code for booking country and terminal location decisions

Country terminal location feature flag: when booking country is in `features.countries.terminal.location` list (default `CI`), booking country is used as terminal location (source: src/main/resources/application.yml:features.countries.terminal.location)
