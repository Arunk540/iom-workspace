---
category: integrations
title: Commodity and Reefer Integration
summary: Integration with master data commodity API and reefer templates API
primary_for: [commodity-api-integration-guide, reefer-client-reference]
mentions: [CommodityClient, ReeferTemperatureClient, V3CommodityCodeValidator, V3CommodityRestrictionValidator, ReferenceDataFetcher]
scenarios:
  - validate commodity code in search request
  - fetch reefer temperature requirements
  - understand commodity restriction validation
  - find commodity api base url
  - find reefer api base url
capabilities: [offer-search, validation]
domains: [offer-management]
entities: [SearchRequest, ContainerRequest]
sources:
  - src/main/kotlin/com/maersk/iom/offer/webclient/commodity/CommodityClient.kt
  - src/main/kotlin/com/maersk/iom/offer/webclient/commodity/ReeferTemperatureClient.kt
  - src/main/kotlin/com/maersk/iom/offer/validator/v3/V3CommodityCodeValidator.kt
  - src/main/kotlin/com/maersk/iom/offer/validator/v3/V3CommodityRestrictionValidator.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [integrations/container-type.md, runtime/offer-search-flow.md, operations/flags-and-lists.md]
---

## Commodity API

### System
IOM Master Data Service — commodities endpoint.

### Configuration

| Property | Value |
|---|---|
| Base URL | `${maersk.masterdata-base-url}commodities` |
| Circuit breaker | `commodityClientInstance` |
(source: src/main/resources/application.yml:services.commodity)

### Client: CommodityClientImpl

Fetches commodity metadata by commodity code. Used in `enrichSearchRequest()` to validate commodity codes in booking equipment (source: src/main/kotlin/com/maersk/iom/offer/webclient/commodity/CommodityClient.kt)

`V3CommodityCodeValidator` validates that each commodity code in `bookingEquipments` is known. On failure, returns error with field path `bookingEquipments[n].commodities[m].commodityCode` (source: src/main/kotlin/com/maersk/iom/offer/validator/v3/V3CommodityCodeValidator.kt)

### Commodity Restrictions (Business Rules)

`V3CommodityRestrictionValidator` calls the business rules / commodity restrictions endpoint to check if a commodity is allowed for the requested trade lane. Base URL: `${maersk.baseUrl}dh/commodity-restrictions/commodity-restrictions`, consumer key `${iomBusinessRulesConsumerKey}` (source: src/main/resources/application.yml:services.commodity-restrictions)

## Reefer Temperature API

### System
Maersk reefer templates API — provides min/max temperature ranges by commodity code.

### Configuration

| Property | Value |
|---|---|
| Base URL | `${maersk.baseUrl}reefer-templates/{commodityCode}` |
| Consumer key | `xXIXujnYnqne1yiEnRfIAY7UMAWsbh7J` |
| Circuit breaker | `reeferTempClientInstance` |
(source: src/main/resources/application.yml:services.reefer)

### Client: ReeferTemperatureClientImpl

Fetches reefer temperature requirements for a commodity code. Used during `enrichSearchRequest()` to validate reefer container temperature settings against commodity requirements (source: src/main/kotlin/com/maersk/iom/offer/webclient/commodity/ReeferTemperatureClient.kt)

`NonOperatingReeferValidator` validates that NOR (non-operating reefer) flag is consistent with commodity temperature requirements (source: src/main/kotlin/com/maersk/iom/offer/validator/v3/NonOperatingReeferValidator.kt)
