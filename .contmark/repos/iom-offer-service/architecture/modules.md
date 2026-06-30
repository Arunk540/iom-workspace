---category: architecture
title: Module Structure
summary: Package and module decomposition of iom-offer-service
primary_for: [module-decomposition-map, package-responsibility-guide]
mentions: [controller, service, webclient, document, domain, config, messaging, validator, mapper, repository]
scenarios:
  - understand package layout
  - find domain model classes
  - locate webclient implementations
  - add new external integration
  - understand validator chain
  - understand module layout
  - find the module structure
  - find a package
capabilities: [offer-search, rate-calculation, routing, kafka-consumption, persistence]
domains: [offer-management, rates, routing]
entities: [OfferedServicePlanDocument, SearchRequestDocument, OfferRequest, SearchRequest]
sources:
  - src/main/kotlin/com/maersk/iom/offer/
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [architecture/cross-cutting.md, navigation/key-classes.md, stack/stack.md]
---

## Package Tree

```
com.maersk.iom.offer
├── controller/v3/          HTTP entry points (5 controllers)
├── service/v3/             Core business logic services
├── service/                Shared services (LocationService, CargoWeightValidationService)
├── webclient/              External HTTP clients
│   ├── offer/v3/           OIPO offer client
│   ├── rates/              Rates v8 client
│   ├── routing/v3/         Routing client
│   ├── cargo/              Cargo weight client
│   ├── commodity/          Commodity + reefer client
│   ├── containertype/      Container type client
│   ├── customer/           Customer identification client
│   ├── customs/            Customs rules client
│   ├── facility/           Facility client
│   ├── location/           Location client
│   ├── salesobject/        Sales object client
│   ├── businessRules/      Business rules client
│   └── audit/              OfferAudit MongoDB entity
├── domain/                 Internal domain objects (SearchRequest, OfferRequest, OfferResponse)
├── document/               MongoDB documents + mappers
│   └── mapper/             toDocument / toV3Model conversions
├── model/dto/              External-facing DTOs
│   ├── rates/              CalculateRatesRequest/Response
│   ├── reprice/            RepriceRequest
│   └── v3/                 V3SearchRequest, V3ContainerRequestModel
├── messaging/              Kafka consumer
├── validator/v3/           Bean-validation validators for search requests
├── mapper/                 V3RequestMapper (DTO → domain)
├── config/                 Spring config beans
├── repository/             Spring Data Mongo repositories
├── filter/                 ObservationWebFilter
└── utils/                  CarrierHaulageEnum, extensions
```

## Dependency Direction

```
controller → service → webclient
           ↘ repository (via service)
domain ← service ← mapper ← controller
document ← service ← repository
```

## Key Modules and Boundaries

### Controller Layer
Handles auth (`@PreAuthorize`), request body deserialization, `@JsonView` selection, and delegates to service layer. No business logic (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt:38)

### Service Layer (`service/v3/`)
Owns all business logic:
- `IOMRoutingAndOfferService` — main orchestrator for search
- `RatesService` — rate calculation pipeline
- `RoutingService` — route fetching + filtering
- `OfferedServicePlanPersistenceService` — all MongoDB write operations

### WebClient Layer (`webclient/`)
Each client wraps a `WebClient` bean, applies circuit breaker, retry, and error handling via `ErrorHandler.configureRetry` (source: src/main/kotlin/com/maersk/iom/offer/webclient/ErrorHandler.kt:configureRetry)

### Document Layer (`document/`)
- `OfferedServicePlanDocument` — `@Document(collection="offered_service_plans")` (source: src/main/kotlin/com/maersk/iom/offer/document/OfferedServicePlanDocument.kt:17)
- `SearchRequestDocument` — `@Document(collection="search_request")`
- `OfferAudit` — audit trail for offer API calls, no explicit collection name

### Domain Layer (`domain/`)
Internal pure Kotlin data classes not exposed externally:
`SearchRequest`, `OfferRequest`, `OfferResponse`, `TimeRangeRequest`

### Validator Layer (`validator/v3/`)
Chain of `IOMSearchValidator` implementations injected as `List<IOMSearchValidator>` into `IOMRoutingAndOfferService` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt:53)
