---
category: domain
title: Ocean booking enrichment model
summary: Ocean booking enrichment builds an inland search request from a TP document by combining GCSS, terminal, location, party, country, commodity, and dangerous-goods data.
primary_for: [ocean-booking-model]
mentions: [ocean booking, tpdoc, bill of lading, dangerous goods]
scenarios:
  - ocean booking model
  - tpdoc enrichment
  - bill of lading details
  - dangerous goods mapping
  - ocean reference flow
capabilities: [domain-model]
domains: [Ocean]
entities: [OceanBookingSearchResponse, BillOfLoadingSummaryResponse, DangerousDetailsResponse]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/service/OceanBookingService.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/OceanAutoBookingService.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - runtime/ocean-booking-enrichment-flow.md
  - integrations/gcss-and-reference-data.md
---
# Ocean booking enrichment model

- `OceanBookingService.getOceanBookingSearchResponseForTpDoc(...)` zips GCSS summary, terminal info, location info, container types, parties, countries, and dangerous-goods details into a single `OceanBookingSearchResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OceanBookingService.kt:77)
- `OceanAutoBookingService.toOfferSearchRequest(...)` converts the ocean aggregate into a BFF `SearchRequest` with origin/destination, parties, container requests, carrier-haulage flag, and ocean reference number. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OceanAutoBookingService.kt:92)
- Dangerous goods are enriched via IMDG substance lookups keyed by UN number and amendment version. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OceanBookingService.kt:115)
