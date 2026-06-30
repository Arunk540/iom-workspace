---
category: integrations
title: Container Type Integration
summary: Integration with master data container-types API for equipment validation
primary_for: [container-type-integration-guide, equipment-validation-reference]
mentions: [ContainerTypeClient, V3ContainerTypeValidator, ReferenceDataFetcher]
scenarios:
  - validate container type in search request
  - find container type api base url
  - understand equipment profile validation
  - find container type circuit breaker
  - understand iso size type validation
capabilities: [offer-search, validation]
domains: [offer-management]
entities: [SearchRequest, ContainerRequest]
sources:
  - src/main/kotlin/com/maersk/iom/offer/webclient/containertype/ContainerTypeClient.kt
  - src/main/kotlin/com/maersk/iom/offer/validator/v3/V3ContainerTypeValidator.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [integrations/commodity.md, runtime/offer-search-flow.md]
---

## System

IOM Master Data Service — container-types endpoint.

## Configuration

| Property | Value |
|---|---|
| Base URL | `${maersk.masterdata-base-url}container-types` |
| Circuit breaker | `containerTypeClientInstance` |
| Retry count | 3, minBackOff 2s, jitter 0.75 |
(source: src/main/resources/application.yml:services.containerType)

## Client: ContainerTypeClientImpl

`ContainerTypeClientImpl` fetches valid container types (ISO size type codes). Circuit breaker: `containerTypeClientInstance` (source: src/main/kotlin/com/maersk/iom/offer/webclient/containertype/ContainerTypeClient.kt)

## Validation

`V3ContainerTypeValidator` validates that each `equipmentProfile.equipmentSizeType.isoSizeTypeCode` in `bookingEquipments` is a known container type (source: src/main/kotlin/com/maersk/iom/offer/validator/v3/V3ContainerTypeValidator.kt)

`V3EmptyContainerValidator` additionally checks that if `emptyContainerPoint` is set, the container number is in the `empty-container` allow-list configured in `application.yml` (source: src/main/resources/application.yml:empty-container)

## Reference Data Cache

Container types are also cached in `ReferenceDataFetcher` for fast in-request lookups. Cache is refreshed on the `api.charge.cron` schedule (`0 0 1 * * ?`) (source: src/main/kotlin/com/maersk/iom/offer/config/ReferenceDataFetcher.kt)
