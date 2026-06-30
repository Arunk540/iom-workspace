---
category: domain
title: Location reference data
summary: Location owns location and facility reference lookup, combining cached persistence with live upstream location and facility enrichment.
primary_for: [location-reference-data]
mentions: [locations, facility, cache, webclient, geoId]
scenarios:
  - trace location search
  - inspect location cache
  - find facility enrichment
  - see location tables
  - find location client
capabilities: [domain-summary]
domains: [location]
entities: [LocationController, LocationService, LocationEntity, LocationCacheEntity]
sources:
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/controller/LocationController.kt
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/service/LocationService.kt
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/location/LocationClient.kt
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/facility/FacilityClient.kt
  - location/location-persistence/src/main/kotlin/com/maersk/iom/master/data/location/persistence/LocationEntity.kt
  - location/location-persistence/src/main/kotlin/com/maersk/iom/master/data/location/persistence/LocationCacheEntity.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - runtime/location-search-flow.md
  - integrations/upstream-location-and-facility.md
  - contracts/db-schemas.md
---

# Scope

- Exposes `GET /locations/{geoId}` and `GET /locations` for geo lookups and filtered search.
- Uses `LocationService` to choose between cache-first reads and upstream enrichment.
- Owns `location`, `locations`, `alternate_codes`, and `alternate_names` tables.

## Data model

- `LocationEntity` models source-master records keyed by `geo_id`.
- `LocationCacheEntity` is a write-through cache keyed by generated `id` with Maersk geo identifiers.
- Alternate codes and alternate names stay in dedicated tables; they are not flattened into `LocationEntity`.

## Runtime shape

- Single-record lookups can read from cache and backfill via `LocationClient` on misses.
- Search requests can pivot to `FacilityClient` first when customer/site context is present.
- Both external clients share a common retry policy and OAuth-capable WebClient configuration.
