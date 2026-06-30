---
category: integrations
title: GCSS and reference-data integrations
summary: Ocean booking flows fan out to GCSS plus facility, location, commodity, container-type, ocean-carrier, country, IMDG, customer, and vendor reference-data services.
primary_for: [gcss-reference-data-integration]
mentions: [gcss, reference data, facility, location, commodities]
scenarios:
  - gcss integration map
  - reference data calls
  - facility backend
  - location backend
  - dangerous goods backend
capabilities: [integration-reference-data]
domains: [Ocean]
entities: [BillOfLoadingSummaryResponse, Facility, LocationClientResponse]
sources:
  - src/main/resources/application.yml
  - src/main/kotlin/com/maersk/iom/webintegrator/config/WebClientConfiguration.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/GcssClient.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/FacilityClient.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/LocationClient.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - runtime/ocean-booking-enrichment-flow.md
  - operations/retries.md
---
# GCSS and reference-data integrations

- GCSS base URL is `services.gcss.base-url`, defaulting to `https://api-stage.maersk.com/gcss`, with OAuth2 client-credentials and consumer key. (source: src/main/resources/application.yml:66)
- Facility, commodity, container-type, ocean-carrier, countries, location, vendor, and customer services are separately configured under `services.*.base-url`. (source: src/main/resources/application.yml:69)
- `GcssClient` reads bill-of-lading summary, parties, and dangerous details from `/tpDocs/...` and `/shipments/.../dangerousDetails`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/GcssClient.kt:15)
- `FacilityClient` supports geo-ID lookup, facility-code lookup, and `/search` for customer facilities. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/FacilityClient.kt:35)
- `LocationClient` supports direct geo-ID fetch and `/locations?cityName&countryCode&maerskRkstCode&type=...` search for terminal/city resolution. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/LocationClient.kt:31)
- Several reference-data clients are cached with `@Cacheable`, including facilities, locations, commodities, container types, ocean carriers, and dangerous-goods details. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/FacilityClient.kt:34)
