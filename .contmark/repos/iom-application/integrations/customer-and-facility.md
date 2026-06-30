---
category: integrations
title: Customer and facility services
summary: Covers the customer-party and facility-lookup integrations used to enrich booking parties and location metadata.
primary_for: [customer-facility-integration]
mentions: [customer client, facility client, party resolver, geo id lookup]
scenarios:
  - party enrichment flow
  - facility lookup path
  - customer service call
  - resolve facility geo ids
  - which client owns parties
capabilities: [integration-mapping]
domains: [reference-data]
entities: [CustomerClient, CustomerFacilityClient, FacilityResolver]
sources:
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/config/WebClientConfiguration.kt
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/customer/CustomerClient.kt
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/facility/CustomerFacilityClient.kt
  - resolvers/src/main/kotlin/com/maersk/iom/resolvers/PartyResolver.kt
  - resolvers/src/main/kotlin/com/maersk/iom/resolvers/FacilityResolver.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - domain/party.md
  - domain/location.md
  - runtime/order-processing.md
---
## Customer-party path

- `WebClientConfiguration.customerClient()` creates an OAuth2-protected customer WebClient and injects a consumer key from `services.customer.consumer-key`. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/config/WebClientConfiguration.kt:60)
- `CustomerClientImpl.getParties()` POSTs to `/status-search` with a `CustomerSearchRequest(customerCodes, isActive=true)` payload and returns an array of customer-party records. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/customer/CustomerClient.kt:20)
- `PartyResolver` collects unique party codes from the aggregate, calls `CustomerClient`, and stores successful matches in `resolvedData.parties`. (source: resolvers/src/main/kotlin/com/maersk/iom/resolvers/PartyResolver.kt:17)

## Facility path

- `WebClientConfiguration.facilityClient()` creates an OAuth2-protected facility WebClient rooted at `services.facility.base-url`. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/config/WebClientConfiguration.kt:98)
- `CustomerFacilityClientImpl.getCustomerFacilitiesByCustomerCodes()` calls `/search?facilityType=CUST&customerCode=...` and returns typed facility responses keyed by geographical details and customer code. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/facility/CustomerFacilityClient.kt:19)
- `FacilityResolver` is feature-gated; when enabled, it extracts geo IDs from service-plan leg facilities, calls `FacilityClient`, and writes resolved geographical details into `resolvedData.locations`. (source: resolvers/src/main/kotlin/com/maersk/iom/resolvers/FacilityResolver.kt:12)
