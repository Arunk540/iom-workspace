---category: domain
title: OfferedServicePlanDocument
summary: MongoDB document storing a fully enriched offered inland service plan
primary_for: [offered-service-plan-mongodb-document, persistence-schema-reference]
mentions: [OfferedServicePlanPersistenceService, OfferedServicePlanDocumentRepository, OfferRecordCleanUpService, ServicePlanDomainEventConsumer]
scenarios:
  - understand mongodb document structure
  - find fields stored per offered plan
  - trace service plan number assignment
  - understand offered plan lifecycle
  - find rates stored on offered plan
  - find the persistence layer
  - check persistence schema
capabilities: [offer-search, rate-calculation, persistence]
domains: [offer-management, rates]
entities: [OfferedServicePlanDocument, ProductOffer, ServicePlanRouting, CalculateRatesApiResponse]
sources:
  - src/main/kotlin/com/maersk/iom/offer/document/OfferedServicePlanDocument.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/OfferedServicePlanPersistenceService.kt
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [domain/SearchRequest.md, contracts/db-schemas.md, runtime/offer-search-flow.md]
---

## Overview

`OfferedServicePlanDocument` is the primary MongoDB document for a proposed service plan. Stored in collection `offered_service_plans`. Implements `Persistable<String>` with `isNew()` always returning `true` to force inserts (source: src/main/kotlin/com/maersk/iom/offer/document/OfferedServicePlanDocument.kt:17)

## Key Fields

| Field | Type | Index | Description |
|---|---|---|---|
| `offeredServicePlanNumber` | `String?` | `@Indexed(unique=true)` | IOM-generated plan number |
| `servicePlanNumber` | `String?` | `@Indexed` | Booking system SP number; null until Kafka event arrives |
| `booking` | `Booking?` | — | Parties, price calculation date, booking details |
| `productOffer` | `ProductOffer?` | — | Products, booking equipments, charges, legs |
| `cargoServiceType` | `String` | — | Default `FCL` |
| `servicePlanStage` | `ServicePlanStatus` | — | Lifecycle stage; default `DRAFT` |
| `routing` | `ServicePlanRouting?` | — | Routing legs and transport modes |
| `transportMode` | `String?` | — | Primary transport mode |
| `searchRequestId` | `String?` | — | Links to `SearchRequestDocument` |
| `isCarrierHaulage` | `Boolean?` | — | Carrier vs merchant haulage |
| `isScmBooking` | `Boolean?` | — | SCM booking flag |
| `triangulation` | `TriangulationTypeEnum?` | — | Triangulation type |
| `zone` | `String?` | — | Zonal pricing zone; used to sort results |
| `cargoOriginRegion` | `String?` | — | Cargo origin region for rates |
| `truckType` | `TruckTypeEnum?` | — | Truck type |
| `rates` | `CalculateRatesApiResponse?` | — | Populated after `calculateRates` call |
| `version` | `String` | — | Schema version; default `Version.LATEST` |
| `createdTime` | `Long?` | — | Epoch millis; used by cleanup query |
| `lastModifiedTime` | `Long?` | — | Updated on each save |

## Lifecycle

```
searchForServicePlans()
  └─ saveProposedServicePlanDocument()   → INSERT (isNew=true, offeredServicePlanNumber generated)
ServicePlanDomainEventConsumer
  └─ updateServicePlanNumber()           → SET servicePlanNumber
RatesController.calculateRates()
  └─ updateProductOffer()                → SET productOffer.products[].bookingEquipments[].charges
OfferRecordCleanUpService
  └─ removeUnmappedOffers()             → DELETE where servicePlanNumber IS NULL AND createdTime < cutoff
```

## Persistence Operations

All persistence via `OfferedServicePlanPersistenceService` using `ReactiveMongoTemplate`:
- `saveProposedServicePlanDocument` — save (source: src/main/kotlin/com/maersk/iom/offer/service/v3/OfferedServicePlanPersistenceService.kt:33)
- `findByOfferedServicePlanNumber` — find by unique index (source: src/main/kotlin/com/maersk/iom/offer/service/v3/OfferedServicePlanPersistenceService.kt:44)
- `updateServicePlanNumber` — link to booking SP (source: src/main/kotlin/com/maersk/iom/offer/service/v3/OfferedServicePlanPersistenceService.kt:53)
- `updateProductOffer` — merge rate charges (source: src/main/kotlin/com/maersk/iom/offer/service/v3/OfferedServicePlanPersistenceService.kt:106)
