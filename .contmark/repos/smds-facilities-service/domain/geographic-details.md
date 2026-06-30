---category: domain
title: Geographic details
summary: Geographic data is modeled as nested location, coordinate, and postal-address objects under each facility response.
primary_for: [smds-geographic-details]
mentions: [geographicdetails, locationdetails, postaladdress, geocoordinates, facilityname]
scenarios:
  - inspect address fields
  - inspect location fields
  - find facility name field
  - inspect city region country
  - inspect nested geography model
  - find geographic details
  - check geographic data
capabilities: [domain-modeling]
domains: [smds-facilities]
entities: [GeographicDetails, LocationDetails, PostalAddress, GeoCoordinates]
sources:
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/domain/model/common/GeographicDetails.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/domain/model/common/LocationDetails.java
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/domain/model/common/PostalAddress.java
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - domain/facility.md
  - contracts/db-schemas.md
---

# Geographic details

## Structure

- `GeographicDetails` nests `LocationDetails`, `GeoCoordinates`, and `PostalAddress`.
- `LocationDetails` carries facility name plus site/city/region/country IDs and RKST identifiers.
- `PostalAddress` captures street, building, building number, suburb, and post code.

## Query relevance

These fields drive search filters in the dynamic query layer: city, country, region, street, building number, facility name, and postal code.

## Related

- [[domain/facility]]
- [[runtime/facility-search-flow]]
