---category: integrations
title: Sales Object Integration
summary: Integration with master data sales-objects API for product and pricing context
primary_for: [sales-object-integration-guide, sales-object-client-reference]
mentions: [SalesObjectClient, IOMRoutingAndOfferService]
scenarios:
  - understand sales object lookup
  - find sales object api base url
  - trace sales object in search
  - find sales object circuit breaker
  - understand product pricing context
capabilities: [offer-search]
domains: [offer-management]
entities: [SearchRequest, SalesObject]
sources:
  - src/main/kotlin/com/maersk/iom/offer/webclient/salesobject/SalesObjectClient.kt
  - src/main/kotlin/com/maersk/iom/offer/model/dto/SalesObject.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [integrations/customer.md, runtime/offer-search-flow.md]
---

## System

IOM Master Data Service — sales-objects endpoint.

## Configuration

| Property | Value |
|---|---|
| Base URL | `${maersk.masterdata-base-url}` |
| Path | `/sales-objects` |
| Circuit breaker | `salesObjectClientInstance` |
| Retry count | 3, minBackOff 2s, jitter 0.75 |
(source: src/main/resources/application.yml:services.sales-object)

## Client: SalesObjectClient

`SalesObjectClient.getSalesObject(salesObject)`:
- POST `SalesObject` body to `/sales-objects`
- Returns `Mono<SalesObject>` with enriched sales object data (source: src/main/kotlin/com/maersk/iom/offer/webclient/salesobject/SalesObjectClient.kt:getSalesObject)
- Circuit breaker: `salesObjectClientInstance`
- Fallback: logs warning and returns error `ExtendedExternalApiException` (source: src/main/kotlin/com/maersk/iom/offer/webclient/salesobject/SalesObjectClient.kt:fallback)

## Usage

`SalesObjectClient` is injected into `IOMRoutingAndOfferService`. During offer search, it is called to retrieve sales object data that determines product eligibility and pricing context. The `SalesObject` model maps equipment groups to product identifiers used in the offer request (source: src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt:52)
