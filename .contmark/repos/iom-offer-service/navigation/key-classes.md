---category: navigation
title: Key Classes
summary: Most important classes for understanding and modifying iom-offer-service
primary_for: [class-navigation-index, developer-orientation-guide]
mentions: [IOMRoutingAndOfferService, RatesService, OfferedServicePlanPersistenceService, RoutingService, OfferService, CustomsService, SearchRequestService, OfferRecordCleanUpService]
scenarios:
  - find where offer search logic lives
  - understand rates calculation flow
  - locate mongodb persistence layer
  - trace customs charge enrichment
  - find kafka consumer logic
  - find a key class
  - locate the right class
  - orient a new developer
  - find a developer starting point
capabilities: [offer-search, rate-calculation, routing, customs, persistence]
domains: [offer-management, rates, routing, customs]
entities: [OfferedServicePlanDocument, SearchRequestDocument, OfferRequest, V3SearchRequest]
sources:
  - src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/OfferedServicePlanPersistenceService.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/RoutingService.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/OfferService.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/CustomsService.kt
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [navigation/entry-points.md, architecture/modules.md, runtime/offer-search-flow.md]
---

## Service Layer

| Class | Package | Role |
|---|---|---|
| `IOMRoutingAndOfferService` | `service.v3` | Orchestrates routing + offer + customs + persistence for the main search flow |
| `RatesService` | `service.v3` | Handles rate calculation and reprice; owns mandatory charge validation |
| `RoutingService` | `service.v3` | Calls routing API per equipment type; merges and filters routes |
| `OfferService` | `service.v3` | Calls offer-v3 API (OIPO); applies mandatory charges validation |
| `CustomsService` | `service.v3` | Fetches customs charges; falls back gracefully on error |
| `SearchRequestService` | `service.v3` | Persists and retrieves `SearchRequestDocument` from MongoDB |
| `OfferedServicePlanPersistenceService` | `service.v3` | Saves, updates, and queries `OfferedServicePlanDocument` in MongoDB |
| `OfferRecordCleanUpService` | `service.v3` | Deletes unmapped offered service plan documents older than N days |
| `CargoWeightValidationService` | `service` | Validates cargo weight against container restrictions |

## Controller Layer

| Class | Mapping | Key Methods |
|---|---|---|
| `IOMSearchQueryController` | `/v3` | `searchServicePlans`, `searchServicePlansToAmend`, `searchServicePlansStream` |
| `RatesController` | `/v3/rates` | `calculateRates` |
| `OfferServiceDetailsController` | `/v3` | `getOfferServicePlanDetails`, `getRepricedOffer`, `deleteEquipmentsFromOfferedServicePlan` |
| `OfferServiceCleanUpController` | `/v3` | `removeOffers` |

## WebClient Layer

| Client | External System | Circuit Breaker |
|---|---|---|
| `OfferClientImpl` | OIPO multicarrier offers v4 | `offerClientInstance` |
| `RatesClientImpl` | OIPO rates v8 | `ratesClientInstance` |
| `RoutingClientImpl` | Routing delivery-network API | `routingClientInstance` |
| `CargoWeightClientImpl` | Cargo-weight rules API | `cargoWeightClientInstance` |
| `CustomsClientImpl` | Customs services API | `customsClientInstance` |
| `SalesObjectClient` | Master data sales-objects | `salesObjectClientInstance` |
| `LocationClient` | Master data locations | `locationClientInstance` |
| `CommodityClient` | Master data commodities | `commodityClientInstance` |
| `FacilityClient` | SMDS facilities service | `facilityClientInstance` |
| `ContainerTypeClient` | Master data container-types | `containerTypeClientInstance` |
| `CustomerClient` | Customer identification API | `customerClientInstance` |
| `BusinessRulesClientImpl` | Business rules / commodity restrictions | (default) |

## Kafka

| Class | Direction | Topic |
|---|---|---|
| `ServicePlanDomainEventConsumer` | INBOUND | `iom-serviceplan-domain-event-topic` |

## Config / Cross-cutting

| Class | Role |
|---|---|
| `SecurityConfig` | JWT validation (Forgerock + Azure AD) |
| `ReferenceDataFetcher` | In-memory cache for charge types, commodity codes |
| `ObservationWebFilter` | Micrometer tracing filter |
| `WebClientConfiguration` | All WebClient bean wiring |
| `MandatoryChargesConfig` | Loads mandatory charge code lists by transport mode |
