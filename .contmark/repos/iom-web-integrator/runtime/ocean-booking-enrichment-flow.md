---
category: runtime
title: Ocean booking enrichment flow
summary: Ocean-reference booking support pulls GCSS bill-of-lading data plus multiple reference-data lookups to synthesize inland search and booking payloads.
primary_for: [ocean-booking-flow]
mentions: [ocean reference, tpdoc lookup, bill of lading, dangerous goods]
scenarios:
  - ocean booking flow
  - tpdoc flow
  - bill of lading flow
  - dangerous goods flow
  - ocean reference debug
capabilities: [ocean-booking]
domains: [Ocean]
entities: [OceanBookingSearchResponse, BillOfLadingPartyResponse, LocationAssociation]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/service/OceanBookingService.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/OceanAutoBookingService.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/GcssClient.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/FacilityClient.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/LocationClient.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - integrations/gcss-and-reference-data.md
  - domain/ocean-booking.md
---
# Ocean booking enrichment flow

- `OceanBookingService.getOceanBookingSearchResponseForTpDoc(...)` starts from GCSS bill-of-lading summary and then zips commodities, terminal info, location info, container types, parties, countries, and dangerous-goods lookups. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OceanBookingService.kt:77)
- GCSS calls cover `/tpDocs/{tpDocNumber}/billOfLadingSummary`, `/tpDocs/{tpDocNumber}/parties`, and `/shipments/{oceanReferenceNumber}/dangerousDetails`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/GcssClient.kt:50)
- Terminal enrichment uses facility-service lookup by geo-site ID, while timezone-aware city/facility enrichment uses location-service lookups. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OceanBookingService.kt:145)
- `OceanAutoBookingService.createBooking(...)` converts the ocean aggregate into a search request, optionally resolves a customer facility, and writes container stuffing/loading/discharge/delivery haulage dates. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OceanAutoBookingService.kt:21)
- Empty-container pickup/drop nodes are added only when `emptyContainerLocationCode` is supplied. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OceanAutoBookingService.kt:75)
