---category: navigation
title: REST entry points — part 2
summary: Sales, vendors, diesel price ranges, and vessel entry points exposed by iom-master-data.
primary_for: [rest-entry-points-extended]
mentions: [sales, vendors, diesel-price-ranges, vessel, controllers, routes]
scenarios:
  - find vendor endpoints
  - trace vessel api
  - see sales routes
  - inspect diesel endpoint
  - map batch lookups
  - list the entry points
capabilities: [entry-point-mapping]
domains: [sales, vendors, diesel-price-ranges, vessel]
entities: [SalesTypesController, VendorController, VendorV2Controller, DieselPriceRangesController, VesselController]
sources:
  - diesel-price-ranges/diesel-price-ranges-controller/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/controller/DieselPriceRangesController.kt
  - sales/sales-controller/src/main/kotlin/com/maersk/iom/master/data/sales/controller/controller/SalesTypesController.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/controller/VendorController.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/controller/VendorV2Controller.kt
  - vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/VesselController.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - navigation/entry-points-1.md
  - contracts/api-contracts-2.md
  - navigation/scenarios.md
---

# Entry points — part 2

## Sales
- `GET /sales-types` — `SalesTypesController.getSalesTypes` — Read-only sales type search. (source: sales/sales-controller/src/main/kotlin/com/maersk/iom/master/data/sales/controller/controller/SalesTypesController.kt:19)
- `POST /sales-objects` — `SalesTypesController.findMatchingSalesObjects` — Sales object matching for container combinations. (source: sales/sales-controller/src/main/kotlin/com/maersk/iom/master/data/sales/controller/controller/SalesTypesController.kt:26)

## Vendors
- `GET /vendors` — `VendorController.getVendors` — Vendor V1 search by request criteria. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/controller/VendorController.kt:29)
- `POST /vendors` — `VendorController.insertOrUpdateVendor` — Direct V1 upsert endpoint alongside Kafka ingest. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/controller/VendorController.kt:39)
- `GET /vendors/batch` — `VendorController.getVendorsByMdgCodesOrCodes` — Batch vendor V1 lookup by MDG or SMDS codes. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/controller/VendorController.kt:49)
- `GET /v2/vendors` — `VendorV2Controller.getVendorsV2` — Vendor V2 search. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/controller/VendorV2Controller.kt:29)
- `POST /v2/vendors` — `VendorV2Controller.insertOrUpdateVendorV2` — Direct V2 upsert endpoint alongside Kafka ingest. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/controller/VendorV2Controller.kt:39)
- `GET /v2/vendors/batch` — `VendorV2Controller.getVendorsV2ByMdgCodesOrCodesOrVmdmCodes` — Batch vendor V2 lookup by VMDM, MDG, or SMDS codes. (source: vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/controller/VendorV2Controller.kt:49)

## Diesel price ranges
- `GET /diesel-price-ranges` — `DieselPriceRangesController.getDieselPriceRanges` — Read-only diesel price range lookup. (source: diesel-price-ranges/diesel-price-ranges-controller/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/controller/DieselPriceRangesController.kt:21)

## Vessel
- `GET /vessels` — `VesselController.` — Search vessels by text and active status. (source: vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/VesselController.kt:39)
- `POST /vessels` — `VesselController.` — Create or update vessel records. (source: vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/VesselController.kt:71)
- `DELETE /vessels/{vesselCode}` — `VesselController.` — Soft-delete/deactivate a vessel. (source: vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/VesselController.kt:106)
