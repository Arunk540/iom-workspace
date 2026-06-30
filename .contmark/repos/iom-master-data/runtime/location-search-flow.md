---
category: runtime
title: Location search and enrichment flow
summary: How location queries choose cache, upstream location lookup, and facility enrichment paths.
primary_for: [location-search-flow]
mentions: [locations, facility, cache, webclient, retry]
scenarios:
  - trace location request
  - debug facility enrichment
  - follow cache fallback
  - inspect location retries
  - see geo search flow
capabilities: [runtime-flow]
domains: [location]
entities: [LocationController, LocationService, LocationClientImpl, FacilityClientImpl]
sources:
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/controller/LocationController.kt
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/service/LocationService.kt
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/location/LocationClient.kt
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/facility/FacilityClient.kt
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/configuration/ErrorHandler.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - integrations/upstream-location-and-facility.md
  - operations/retries.md
  - domain/location.md
---

# Flow

1. `GET /locations/{geoId}` enters `LocationController.getLocation` and forwards the path variable to `LocationService.findByMaerskGeoLocationId`. (source: location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/controller/LocationController.kt:35; location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/service/LocationService.kt:30)
2. When write-through cache is enabled, `LocationService` reads `LocationEntityRepositoryCache` first and only calls `LocationClient.getLocation` on a miss, then saves the fetched record back into cache. (source: location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/service/LocationService.kt:31)
3. `GET /locations` calls `LocationService.findLocationsBasedOnFilterCriteria`, which branches to facility-first search when `type=CUSTOMER` with `cityName` or any `siteName` is present. (source: location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/controller/LocationController.kt:48; location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/service/LocationService.kt:51)
4. The facility-first branch calls `FacilityClient.retrieveFacilities`, deduplicates `facilityGeoId`, and enriches each facility with `LocationClient.getLocation` for timezone/type details. (source: location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/service/LocationService.kt:60)
5. The non-facility branch fans out to `LocationClient.retrieveLocations` using `cityName` and optionally `maerskRkstCode`, then deduplicates the combined results by RKST or geo ID. (source: location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/service/LocationService.kt:90; location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/location/LocationClient.kt:41)
6. `LocationClient` and `FacilityClient` both route 5xx responses through `ErrorHandler`, which applies backoff retry only for `ExternalApiException` failures. (source: location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/location/LocationClient.kt:32; location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/facility/FacilityClient.kt:44; location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/configuration/ErrorHandler.kt:38)
