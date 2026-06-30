---
category: contracts
title: Database Schemas
summary: MongoDB collections and their document structure in iom-offer-service
primary_for: [mongodb-schema-reference, collection-index-guide]
mentions: [OfferedServicePlanDocument, SearchRequestDocument, OfferAudit, OfferedServicePlanPersistenceService, SearchRequestService]
scenarios:
  - understand mongodb collection structure
  - find indexes on offered service plans
  - understand audit collection schema
  - find how search request is stored
  - check document fields in mongodb
capabilities: [persistence]
domains: [offer-management]
entities: [OfferedServicePlanDocument, SearchRequestDocument, OfferAudit]
sources:
  - src/main/kotlin/com/maersk/iom/offer/document/OfferedServicePlanDocument.kt
  - src/main/kotlin/com/maersk/iom/offer/document/SearchRequestDocument.kt
  - src/main/kotlin/com/maersk/iom/offer/webclient/audit/entity/OfferAudit.kt
  - src/main/resources/application.yml
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [domain/OfferedServicePlanDocument.md, domain/SearchRequest.md, operations/failure-model.md]
---

## Database

- **Host**: `${mongodbhost}:${mongodbport}` (default: `localhost:27017`)
- **Database name**: `iom-offer` (`${mongodbname}`)
- **Driver**: Spring Data MongoDB Reactive via `ReactiveMongoTemplate`
- **Connection pool**: `minPoolSize=1`, `maxIdleTimeMS=120000`
- **Timeout**: `serverSelectionTimeoutMS=100`, `connectTimeoutMS=100`
(source: src/main/resources/application.yml:spring.data.mongodb)

## Collection: offered_service_plans

Mapped by `OfferedServicePlanDocument` (source: src/main/kotlin/com/maersk/iom/offer/document/OfferedServicePlanDocument.kt:17)

### Indexes

| Field | Type | Notes |
|---|---|---|
| `offeredServicePlanNumber` | Unique | Primary lookup key |
| `servicePlanNumber` | Non-unique | Used for Kafka update query |

### Key Fields

| Field | Description |
|---|---|
| `offeredServicePlanNumber` | IOM-generated unique plan ID |
| `servicePlanNumber` | Booking SP number; `null` until Kafka event |
| `booking` | Parties, priceCalculationDate |
| `productOffer` | Products, equipments, charges, legs |
| `routing` | ServicePlanRouting with legs |
| `searchRequestId` | Foreign key to `search_request` |
| `servicePlanStage` | Lifecycle stage (default `DRAFT`) |
| `createdTime` | Epoch millis; used by cleanup filter |
| `rates` | `CalculateRatesApiResponse` after rates call |
| `version` | Schema version |

### Queries Used

- `findOne` by `offeredServicePlanNumber` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/OfferedServicePlanPersistenceService.kt:44)
- `updateFirst` to set `servicePlanNumber` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/OfferedServicePlanPersistenceService.kt:64)
- `updateFirst` to set `productOffer` (rates merge) (source: src/main/kotlin/com/maersk/iom/offer/service/v3/OfferedServicePlanPersistenceService.kt:106)
- `remove` where `createdTime < cutoff AND servicePlanNumber IS NULL` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/OfferRecordCleanUpService.kt:18)

## Collection: search_request

Mapped by `SearchRequestDocument` (source: src/main/kotlin/com/maersk/iom/offer/document/SearchRequestDocument.kt)

### Key Fields

| Field | Description |
|---|---|
| `searchRequestId` | UUID; primary lookup key |
| `origin` / `destination` | Location associations |
| `parties` | Party list |
| `bookingEquipments` | Container requests |
| `transportActivity` | EXPORT or IMPORT |
| `servicePlanNumber` | Present in amend flow |

### Queries Used

- `save` on each search (source: src/main/kotlin/com/maersk/iom/offer/service/v3/SearchRequestService.kt)
- `findOne` by `searchRequestId` for reprice and details endpoints

## Collection: OfferAudit (no explicit name)

Mapped by `OfferAudit` — no `collection=` attribute, so Spring Data uses class name as collection name. Stores offer API request/response pairs. Only written when `features.offers.audit.enabled=true` (source: src/main/kotlin/com/maersk/iom/offer/webclient/audit/entity/OfferAudit.kt)

| Field | Description |
|---|---|
| `correlationId` | Links to search flow |
| `timestamp` | Instant of call |
| `request` | `V3OfferRequestModel` |
| `responseStatus` | HTTP status code |
| `responseBody` | `MultiCarrierOfferResponseV3` |
| `errorMessage` | Error string if failed |
