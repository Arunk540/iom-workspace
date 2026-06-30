---category: navigation
title: Key classes by concern
summary: High-signal classes for controllers, service logic, persistence, Kafka, and cross-cutting configuration.
primary_for: [key-class-index]
mentions: [controllers, services, repositories, webclient, kafka, config]
scenarios:
  - find important classes
  - see service owners
  - locate kafka code
  - locate configs
  - map persistence gateways
  - find a key class
capabilities: [class-index]
domains: [service-misc, charges, commodity, container, location, sales, vendors, diesel-price-ranges, vessel]
entities: [SecurityConfig, LocationService, CommodityServiceImpl, VendorV2KafkaConfig]
sources:
  - service/src/main/kotlin/com/maersk/iom/master/data/config/SecurityConfig.kt
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/service/LocationService.kt
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/service/CommodityServiceImpl.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/config/VendorV2KafkaConfig.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - navigation/scenarios.md
  - architecture/modules.md
  - operations/monitoring.md
---

# Key classes

| Concern | Class |
|---|---|
| Application boot | `IomMasterDataApplication` |
| Security rules | `SecurityConfig` |
| OpenAPI wiring | `SwaggerConfig` |
| Observability AOP | `ObservedAspectConfiguration` |
| Feature flags | `LaunchDarklyLocalConfig` |
| Location orchestration | `LocationService` |
| Upstream location API | `LocationClientImpl` |
| Upstream facility API | `FacilityClientImpl` |
| Commodity orchestration | `CommodityServiceImpl` |
| Commodity restrictions API | `CommodityRestrictionClientImpl` |
| Container warm cache | `ContainerLoadService` |
| Charge enrichment | `ChargeTypeServiceImpl` |
| VAT partner lookup | `VatPartnerServiceImpl` |
| Vendor V1 ingest | `VendorDataConsumer` |
| Vendor V2 ingest | `VendorV2DataConsumer` |
| Vendor V2 Kafka tuning | `VendorV2KafkaConfig` |
| Vessel CRUD | `VesselServiceImpl` |
| Sales object matching | `SalesTypesServiceImpl` |
| Diesel lookup | `DieselPriceRangesServiceImpl` |
| VAS persistence | `VasServiceImpl` |
