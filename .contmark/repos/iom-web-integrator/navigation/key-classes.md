---
category: navigation
title: Key classes by concern
summary: Quick concern-to-class routing for the main controllers, services, mappers, config, and clients.
primary_for: [key-class-map]
mentions: [navigation, classes, concerns]
scenarios:
  - find key class
  - which class owns
  - where logic lives
  - map concern class
  - debug by concern
capabilities: [navigation]
domains: [Web Integrator]
entities: [OrderService, OfferService, SecurityConfig]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - architecture/modules.md
  - navigation/scenarios.md
---
# Key classes by concern

| concern | class |
| --- | --- |
| auth and JWT validation | `config/SecurityConfig` |
| outbound client wiring | `config/WebClientConfiguration` |
| global exception mapping | `exception/WebIntegratorExceptionHandler` |
| booking entrypoint | `controller/BookingController` |
| offer search and repricing | `controller/OfferController`, `service/OfferService` |
| order lifecycle | `controller/OrderController`, `service/OrderService` |
| business rules | `controller/BusinessRuleController`, `service/BusinessRuleService` |
| ocean booking enrichment | `service/OceanBookingService`, `service/OceanAutoBookingService` |
| downstream order transport | `webclient/OrderClient` |
| downstream offer transport | `webclient/OfferClient` |
| billing side effects | `webclient/BillingClient` |
| master/reference data | `webclient/FacilityClient`, `LocationClient`, `CommodityClient`, `CountriesClient` |
| CCD contract lookup | `controller/CcdSearchController`, `webclient/CcdSearchClient` |
| user preferences | `controller/UserPreferencesController`, `webclient/UserPreferencesClient` |
| major DTO mapping | `mapper/V3ServicePlanMapper`, `V3SearchRequestMapper`, `V3OrderRequestMapper` |
