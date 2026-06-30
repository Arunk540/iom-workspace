---
category: domain
title: Location and leg endpoints
summary: Owns facilities, cities, postal addresses, alternative codes, and the location-association wrapper used across routing and service-plan legs.
primary_for: [location-domain-model]
mentions: [facility model, city model, postal address, geo id]
scenarios:
  - location domain model
  - facility and city types
  - geo id lookup
  - service plan leg locations
  - postal address structure
capabilities: [location-modeling]
domains: [order-management]
entities: [LocationAssociation, Facility, City]
sources:
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/models/Location.kt
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/models/ServicePlanLeg.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - domain/service-plan.md
  - integrations/customer-and-facility.md
---
## Core shape

- `LocationAssociation` couples a location function with either a facility or city, optional leg type/time range, and references.
- `Facility` keeps facility code, country/city links, contact info, timezone, postal address, and alternative codes.
- `City` keeps ISO country, city identifiers, names, timezone, region, and UN/LOCODE data.
- `PostalAddress`, `TelecommunicationNumbers`, and `AlternativeCode` are shared supporting value objects.

## Important behaviors

- `fetchLocationGeoId()` prefers facility geo IDs and falls back to city geo IDs.
- `Facility.isManuallyAdded()` distinguishes user-entered facilities from mastered ones.
- `ServicePlanLeg` uses location pairs to derive matching, alternate-code checks, and event-trigger-specific location lookup.
