---
category: integrations
title: Charge Types Integration
summary: Integration with master data charge-types API and in-memory charge type cache
primary_for: [charge-types-integration-guide, charge-type-cache-reference]
mentions: [ReferenceDataFetcher, CustomsService, ChargeStandardizations]
scenarios:
  - understand charge type name resolution
  - find charge types api base url
  - trace charge type cache refresh
  - understand mandatory charge codes
  - find charge standardization logic
capabilities: [offer-search, rate-calculation]
domains: [offer-management, rates]
entities: [V3ChargeModel, V3ChargeType]
sources:
  - src/main/kotlin/com/maersk/iom/offer/config/ReferenceDataFetcher.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/ChargeStandardizations.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [integrations/customs.md, runtime/rates-flow.md, operations/flags-and-lists.md]
---

## System

IOM Master Data Service — charge-types endpoint.

## Configuration

| Property | Value |
|---|---|
| Base URL | `${maersk.masterdata-base-url}charge-types` |
| Cache refresh cron | `0 0 1 * * ?` (daily at 01:00) |
| Cache enabled | `${api.cache.enabled:true}` |
(source: src/main/resources/application.yml:services.charge)

## Reference Data Cache: ReferenceDataFetcher

`ReferenceDataFetcher` loads charge type data at startup and on cron schedule. Provides:
- `getCacheChargeType(materialCode)` — returns `ChargeType` with `name` and `code`
- Used by `CustomsService.getChargeTypeName(materialCode)` to resolve human-readable charge names for customs charges (source: src/main/kotlin/com/maersk/iom/offer/service/v3/CustomsService.kt:getChargeTypeName)

If a material code is not in cache, `CustomsService` throws `ExtendedExternalApiException` (BAD_GATEWAY) rather than returning a null name (source: src/main/kotlin/com/maersk/iom/offer/service/v3/CustomsService.kt:getChargeTypeName)

## Charge Standardizations

`ChargeStandardizations` in `service.v3` provides logic to standardize charge names:
- `updatePreferredChargeName()` in `IOMRoutingAndOfferService` applies charge name standardization from the cache to each `V3ChargeModel` in a service plan before saving (source: src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt:searchForServicePlans)
(source: src/main/kotlin/com/maersk/iom/offer/service/v3/ChargeStandardizations.kt)

## Mandatory Charge Code Configuration

Mandatory charge codes are loaded from `application.yml` via `MandatoryChargesConfig`/`MandatoryChargesProperties`:

| Direction | Mode | Codes |
|---|---|---|
| IMPORT | Truck | `100178, 100318, 109706, 100267, 100405, 100986, 101342, 100430` |
| IMPORT | Rail | `100135` |
| IMPORT | Barge | `106522` |
| EXPORT | Truck | `100317, 109707, 100384, 100177, 100405, 100986, 100430` |
| EXPORT | Rail | `100135` |
| EXPORT | Barge | `106522` |
| Manual | — | `100986, 101342` |
(source: src/main/resources/application.yml:mandatory-charges)
