---
category: integrations
title: Business Rules Integration
summary: Integration with business rules API for commodity restrictions and cargo weight
primary_for: [business-rules-api-integration-guide, commodity-restriction-reference]
mentions: [BusinessRulesClientImpl, V3CommodityRestrictionValidator, CargoWeightClientImpl, CargoWeightValidationService]
scenarios:
  - validate commodity restrictions
  - understand business rules api usage
  - find business rules base url
  - trace commodity restriction check
  - find cargo weight rules endpoint
capabilities: [offer-search, validation]
domains: [offer-management]
entities: [SearchRequest, ContainerRequest]
sources:
  - src/main/kotlin/com/maersk/iom/offer/webclient/businessRules/BusinessRulesClient.kt
  - src/main/kotlin/com/maersk/iom/offer/validator/v3/V3CommodityRestrictionValidator.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [integrations/cargo-weight.md, integrations/commodity.md, runtime/offer-search-flow.md]
---

## System

Maersk `dh` (data hub) business rules domain.

## Configuration

| Property | Value |
|---|---|
| Base URL | `${maersk.baseUrl}dh` |
| Consumer key | `${iomBusinessRulesConsumerKey}` = `XM06yderfN2tqttAlMqvohEuAXAFPx8r` |
| Commodity restrictions sub-path | `commodity-restrictions/commodity-restrictions` |
| Cargo weight sub-path | `container-and-cargo-weight` |
(source: src/main/resources/application.yml:services.businessRules)

## Client: BusinessRulesClientImpl

`BusinessRulesClientImpl` calls the business rules API for commodity restriction validation. Used in `V3CommodityRestrictionValidator` during the search request validator chain (source: src/main/kotlin/com/maersk/iom/offer/webclient/businessRules/BusinessRulesClient.kt)

Retry via `ErrorHandler.configureRetry`; default circuit breaker config.

## Commodity Restrictions

`V3CommodityRestrictionValidator` checks whether a commodity is restricted for the requested trade lane / origin-destination pair. Called during `enrichSearchRequest()` / `validateSearchRequestBasicValidator()` (source: src/main/kotlin/com/maersk/iom/offer/validator/v3/V3CommodityRestrictionValidator.kt)

Business rules consumer key also applies to SAP TMS rules validation (currently feature-flagged off: `features.sap.tms.rules.validation.active=false`) (source: src/main/resources/application.yml:features.sap.tms.rules.validation.active)
