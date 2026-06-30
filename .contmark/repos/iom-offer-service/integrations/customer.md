---
category: integrations
title: Customer Integration
summary: Integration with customer identification API for party validation
primary_for: [customer-api-integration-guide, party-validation-reference]
mentions: [CustomerClient, V3PartyCodeValidator, V3PartyMasterRoleValidator, V3BookedByContactPartyValidator]
scenarios:
  - validate customer party code
  - find customer api base url
  - understand party validation chain
  - find customer circuit breaker
  - trace bookedby contact validation
capabilities: [offer-search, validation]
domains: [offer-management]
entities: [SearchRequest, V3SearchRequest]
sources:
  - src/main/kotlin/com/maersk/iom/offer/webclient/customer/CustomerClient.kt
  - src/main/kotlin/com/maersk/iom/offer/validator/v3/V3PartyCodeValidator.kt
  - src/main/kotlin/com/maersk/iom/offer/validator/v3/V3PartyMasterRoleValidator.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [integrations/sales-object.md, runtime/offer-search-flow.md]
---

## System

Maersk customer identification API (export documentation service).

## Configuration

| Property | Value |
|---|---|
| Base URL | `${maersk.customer-baseUrl}export-documentation/customer-identification/api/v1/customer/` |
| Consumer key | `h7LS17A9zArNFz3eTb0InmYxwS1afIhw` |
| Circuit breaker | `customerClientInstance` |
(source: src/main/resources/application.yml:services.customer)

## Client: CustomerClientImpl

`CustomerClientImpl` fetches customer details by party/customer code. Used in `enrichSearchRequest()` to validate that `BOOKED_BY` party and other parties are known customers (source: src/main/kotlin/com/maersk/iom/offer/webclient/customer/CustomerClient.kt)

Circuit breaker: `customerClientInstance`; retry via `ErrorHandler.configureRetry`

## Validators

### V3PartyCodeValidator
Validates that all party codes in `parties` list are valid customer codes by calling `CustomerClient` (source: src/main/kotlin/com/maersk/iom/offer/validator/v3/V3PartyCodeValidator.kt)

### V3PartyMasterRoleValidator
Validates that required party master roles (e.g., `BOOKED_BY`) are present and only appear once in the parties list (source: src/main/kotlin/com/maersk/iom/offer/validator/v3/V3PartyMasterRoleValidator.kt)

### V3BookedByContactPartyValidator
Validates that the `BOOKED_BY` party has valid contact information (source: src/main/kotlin/com/maersk/iom/offer/validator/v3/V3BookedByContactPartyValidator.kt)
