---
category: navigation
title: REST entry points — part 1
summary: Service, charges, commodity, container, and location entry points exposed by iom-master-data.
primary_for: [rest-entry-points-map]
mentions: [controllers, webflux, routes, service-misc, charges, commodity, container, location]
scenarios:
  - find api route
  - trace incoming request
  - map controller endpoints
  - locate rest handler
  - see deprecated aliases
capabilities: [entry-point-mapping]
domains: [service-misc, charges, commodity, container, location]
entities: [ReasonController, ChargesController, CommodityController, ContainerTypeController, LocationController]
sources:
  - charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/ChargesController.kt
  - charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/VatPartnerController.kt
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/CommodityController.kt
  - container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/ContainerTypeController.kt
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/controller/LocationController.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/carrier/controller/OceanCarrierController.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/country/controller/CountryController.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/currency/controller/CurrencyController.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/customs/controller/CustomsController.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/emergencyTemp/EmergencyTemperatureController.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/finops/controller/FinopsCarrierController.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/imoclassification/controller/IMOClassificationController.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/layouttypes/controller/LayoutTypesController.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/reasons/controller/ReasonController.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/vas/controller/VasController.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - navigation/entry-points-2.md
  - contracts/api-contracts-1.md
  - navigation/scenarios.md
---

# Entry points — part 1

## Service-hosted reference domains
- `GET /ocean-carriers` — `OceanCarrierController.getOceanCarriers` — Ocean carrier list. (source: service/src/main/kotlin/com/maersk/iom/master/data/carrier/controller/OceanCarrierController.kt:24)
- `GET /countries` — `CountryController.getCountries` — Country reference list. (source: service/src/main/kotlin/com/maersk/iom/master/data/country/controller/CountryController.kt:22)
- `GET /currencies` — `CurrencyController.getCurrencies` — Currency reference list. (source: service/src/main/kotlin/com/maersk/iom/master/data/currency/controller/CurrencyController.kt:27)
- `GET /customs-products` — `CustomsController.getCustomProducts` — Customs product reference list. (source: service/src/main/kotlin/com/maersk/iom/master/data/customs/controller/CustomsController.kt:22)
- `GET /control-emergency-temperatures` — `EmergencyTemperatureController.getEmergencyTemperatureControls` — Emergency temperature reference data. (source: service/src/main/kotlin/com/maersk/iom/master/data/emergencyTemp/EmergencyTemperatureController.kt:22)
- `GET /carriers` — `FinopsCarrierController.getCarriers` — FinOps carrier list. (source: service/src/main/kotlin/com/maersk/iom/master/data/finops/controller/FinopsCarrierController.kt:21)
- `GET /imo-classifications` — `IMOClassificationController.getImoClassifications` — IMO classification list. (source: service/src/main/kotlin/com/maersk/iom/master/data/imoclassification/controller/IMOClassificationController.kt:17)
- `GET /layout-types` — `LayoutTypesController.getLayoutTypes` — Layout type list. (source: service/src/main/kotlin/com/maersk/iom/master/data/layouttypes/controller/LayoutTypesController.kt:24)
- `GET /reasons/amendment` — `ReasonController.getAmendmentReasons` — Amendment reasons from classpath JSON. (source: service/src/main/kotlin/com/maersk/iom/master/data/reasons/controller/ReasonController.kt:32)
- `GET /reasons/cancellation` — `ReasonController.getCancellationReasons` — Cancellation reasons from classpath JSON. (source: service/src/main/kotlin/com/maersk/iom/master/data/reasons/controller/ReasonController.kt:37)
- `GET /reasons/reopen` — `ReasonController.getReopenReasons` — Reopen reasons from classpath JSON. (source: service/src/main/kotlin/com/maersk/iom/master/data/reasons/controller/ReasonController.kt:42)
- `GET /reasons/invoice-cancel` — `ReasonController.getInvoiceCancelReasons` — Invoice cancel reasons from classpath JSON. (source: service/src/main/kotlin/com/maersk/iom/master/data/reasons/controller/ReasonController.kt:47)
- `GET /reasons/job-close` — `ReasonController.getJobCloseReasons` — Job close reasons from classpath JSON. (source: service/src/main/kotlin/com/maersk/iom/master/data/reasons/controller/ReasonController.kt:52)
- `GET /vas` — `VasController.getVasList` — Deprecated VAS alias backed by local JSON. (source: service/src/main/kotlin/com/maersk/iom/master/data/vas/controller/VasController.kt:27)
- `GET /value-added-services` — `VasController.getValueAddedServices` — VAS response backed by VasService and DB-backed entity mapping. (source: service/src/main/kotlin/com/maersk/iom/master/data/vas/controller/VasController.kt:35)

## Charges
- `GET /chargeTypes` — `ChargesController.getChargeTypesDeprecated` — Deprecated charge type lookup alias. (source: charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/ChargesController.kt:20)
- `GET /charge-types` — `ChargesController.getChargeTypes` — Charge type search with customer, channel, and enrichment filters. (source: charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/ChargesController.kt:28)
- `GET /vatPartners` — `VatPartnerController.getAllDeprecated` — Deprecated VAT partner collection alias. (source: charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/VatPartnerController.kt:21)
- `GET /vatPartners/{vatPartner}` — `VatPartnerController.getSpecificVatPartnerDeprecated` — Deprecated VAT partner detail alias. (source: charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/VatPartnerController.kt:27)
- `GET /vat-partners` — `VatPartnerController.getAll` — VAT partners, optionally filtered by country. (source: charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/VatPartnerController.kt:35)
- `GET /v2/vat-partners` — `VatPartnerController.getAllVatPartnersWithDefault` — VAT partners plus the default country mapping. (source: charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/VatPartnerController.kt:46)
- `GET /vat-partners/{vatPartner}` — `VatPartnerController.getSpecificVatPartner` — Single VAT partner by code. (source: charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/VatPartnerController.kt:57)

## Commodity
- `GET /commodities/HSCommodities/{HSCommodityCode}` — `CommodityController.getDeprecated` — Deprecated HS commodity lookup alias. (source: commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/CommodityController.kt:38)
- `GET /commodities/hs-commodities/{hsCommodityCode}` — `CommodityController.get` — HS commodity by 6-digit code. (source: commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/CommodityController.kt:48)
- `GET /commodities` — `CommodityController.getCommoditiesBySearchParams` — Commodity search by name, code, group, cargo type, and military flag. (source: commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/CommodityController.kt:60)
- `POST /commodities` — `CommodityController.getNonRestrictedCommodities` — Commodity search with restriction filtering request body. (source: commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/CommodityController.kt:101)

## Container
- `GET /containerTypes/{ISOContainerSizeTypeCd}` — `ContainerTypeController.getDeprecated` — Deprecated container type lookup alias. (source: container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/ContainerTypeController.kt:24)
- `GET /containerTypes` — `ContainerTypeController.getContainerTypesDeprecated` — Deprecated container type collection alias. (source: container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/ContainerTypeController.kt:37)
- `GET /container-types/{isoContainerSizeTypeCd}` — `ContainerTypeController.get` — Single container type by ISO code. (source: container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/ContainerTypeController.kt:50)
- `GET /container-types` — `ContainerTypeController.getContainerTypes` — Container type search with operational flags. (source: container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/ContainerTypeController.kt:61)

## Location
- `GET /locations/{geoId}` — `LocationController.getLocation` — Single location by Maersk geo ID. (source: location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/controller/LocationController.kt:35)
- `GET /locations` — `LocationController.getLocationsByCityAndType` — Location/facility search with enrichment and cache fallback. (source: location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/controller/LocationController.kt:48)
