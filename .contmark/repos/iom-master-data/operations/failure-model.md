---category: operations
title: Failure model and degradation behavior
summary: The service mixes fail-fast not-found semantics with selective graceful degradation around facility enrichment and vendor batch ingest.
primary_for: [failure-model-guide]
mentions: [not-found, degradation, batch-errors, soft-delete, fallback]
scenarios:
  - see failure behavior
  - inspect graceful degradation
  - find batch error policy
  - see soft delete semantics
  - trace not-found responses
  - understand the failure model
capabilities: [operations-guide]
domains: [platform, location, commodity, vendors, vessel, diesel-price-ranges]
entities: [LocationService, VendorV2DataConsumer, VesselServiceImpl, DieselPriceRangesController]
sources:
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/service/LocationService.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorV2DataConsumer.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorServiceImpl.kt
  - vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/service/VesselServiceImpl.kt
  - diesel-price-ranges/diesel-price-ranges-controller/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/controller/DieselPriceRangesController.kt
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/service/CommodityServiceImpl.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - operations/retries.md
  - runtime/vendor-ingest-flow.md
  - runtime/location-search-flow.md
---

# Failure behavior

- Most read APIs emit `NotFoundException` when a filtered result is empty, including commodity, charge, diesel, vendor, and vessel lookups. (source: commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/service/CommodityServiceImpl.kt:27; vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/service/VesselServiceImpl.kt:27; diesel-price-ranges/diesel-price-ranges-controller/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/controller/service/DieselPriceRangesServiceImpl.kt:18)
- Location search degrades gracefully when facility enrichment fails for a single facility or when facility lookup returns 404, returning partial/empty results instead of failing the entire request. (source: location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/service/LocationService.kt:62; location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/facility/FacilityClient.kt:45)
- Vendor V2 batch ingest intentionally suppresses per-record failures with `onErrorResume { Mono.empty() }`, so one bad message does not block the rest of the batch. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorV2DataConsumer.kt:44)
- Vessel deletion is soft-delete only and treats already-inactive rows as an error condition instead of silently succeeding. (source: vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/service/VesselServiceImpl.kt:49)
- Diesel queries reject half-specified key pairs with `400 BAD_REQUEST` rather than applying a fallback. (source: diesel-price-ranges/diesel-price-ranges-controller/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/controller/DieselPriceRangesController.kt:39)
