---category: navigation
title: Task scenarios to start classes
summary: Common operator and maintainer phrases mapped to the first class or method worth opening.
primary_for: [task-to-class-routing]
mentions: [start-here, scenarios, controllers, services, consumers]
scenarios:
  - where to change vendors
  - trace commodity restriction
  - debug location search
  - find vessel logic
  - start from charge lookup
  - find a key class
  - locate the right class
capabilities: [task-routing]
domains: [service-misc, charges, commodity, container, location, sales, vendors, diesel-price-ranges, vessel]
entities: [LocationService.findLocationsBasedOnFilterCriteria, CommodityRestrictionClientImpl.getCommodityRestrictions, VendorV2DataConsumer.consumeVendorV2Data]
sources:
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/service/LocationService.kt
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/service/CommodityServiceImpl.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorV2DataConsumer.kt
  - charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/service/ChargeTypeServiceImpl.kt
  - vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/service/VesselServiceImpl.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - navigation/key-classes.md
  - runtime/location-search-flow.md
  - runtime/vendor-ingest-flow.md
---

# Scenario routing

- `debug location cache miss` → `LocationService.findByMaerskGeoLocationId`
- `trace facility enrichment` → `LocationService.findLocationsBasedOnFilterCriteria`
- `inspect upstream location call` → `LocationClientImpl.getLocation`
- `inspect facility call` → `FacilityClientImpl.retrieveFacilities`
- `trace commodity restriction filter` → `CommodityServiceImpl.getNonRestrictedCommodities`
- `find restricted commodity client` → `CommodityRestrictionClientImpl.getCommodityRestrictions`
- `refresh commodity startup data` → `CommodityLoadService`
- `check charge enrichment` → `ChargeTypeServiceImpl.getChargeTypes`
- `find default vat partner logic` → `ChargeTypeDefaultVatPartnerLoadService`
- `trace vat partner lookup` → `VatPartnerServiceImpl.getAllVatPartnerWithDefault`
- `inspect container cache` → `ContainerLoadService.getContainerTypes`
- `trace sales object matching` → `SalesTypesServiceImpl.findMatchingSalesObjects`
- `debug vendor v1 ingest` → `VendorDataConsumer.consumeVendorData`
- `debug vendor v2 ingest` → `VendorV2DataConsumer.consumeVendorV2Data`
- `trace vendor source persistence` → `VendorServiceImpl.saveVendorDataFeed`
- `trace vendor v2 persistence` → `VendorV2ServiceImpl.saveVendorV2DataFeed`
- `inspect vessel search` → `VesselServiceImpl.searchVessels`
- `inspect vessel deactivation` → `VesselServiceImpl.deactivateVessel`
- `inspect diesel query` → `DieselPriceRangesServiceImpl.getByCountryCodeAndCustomerCode`
- `inspect vas lookup` → `VasController.getValueAddedServices`
