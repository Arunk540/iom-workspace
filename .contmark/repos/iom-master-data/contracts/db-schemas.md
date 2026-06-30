---
category: contracts
title: Database schemas
summary: Reactive table contracts inferred from repository entities across all domains using Postgres-backed R2DBC persistence.
primary_for: [postgres-table-map]
mentions: [postgres, r2dbc, tables, entities, liquibase]
scenarios:
  - see table schema
  - find entity columns
  - map database tables
  - inspect vendor tables
  - inspect location tables
capabilities: [db-contracts]
domains: [platform, charges, commodity, container, location, sales, diesel-price-ranges, vendors, vessel, service-misc]
entities: [ChargeTypeEntity, CustomerToChargeTypeEntity, VatPartnerEntity, CountryToVatPartnerEntity, ChargeTypeDefaultVatPartnerEntity, CommodityByCodeEntity, HSCommodityByCodeEntity, ContainerTypeEntity]
sources:
  - charges/charges-persistence/src/main/kotlin/com/maersk/iom/master/data/charges/persistance/ChargeTypeEntity.kt
  - charges/charges-persistence/src/main/kotlin/com/maersk/iom/master/data/charges/persistance/CustomerToChargeTypeEntity.kt
  - charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/service/vatpartner/VatPartnerEntity.kt
  - charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/service/vatpartner/CountryToVatPartnerEntity.kt
  - charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/service/vatpartner/ChargeTypeDefaultVatPartnerEntity.kt
  - commodity/commodity-persistence/src/main/kotlin/com/maersk/iom/master/data/commodity/persistence/CommodityByCodeEntity.kt
  - commodity/commodity-persistence/src/main/kotlin/com/maersk/iom/master/data/commodity/persistence/HSCommodityByCodeEntity.kt
  - container/container-persistence/src/main/kotlin/com/maersk/iom/master/data/container/persistence/ContainerTypeEntity.kt
  - location/location-persistence/src/main/kotlin/com/maersk/iom/master/data/location/persistence/LocationEntity.kt
  - location/location-persistence/src/main/kotlin/com/maersk/iom/master/data/location/persistence/LocationCacheEntity.kt
  - location/location-persistence/src/main/kotlin/com/maersk/iom/master/data/location/persistence/AlternateCodeEntity.kt
  - location/location-persistence/src/main/kotlin/com/maersk/iom/master/data/location/persistence/AlternateNameEntity.kt
  - sales/sales-persistence/src/main/kotlin/com/maersk/iom/master/data/persistence/SalesTypeEntity.kt
  - sales/sales-persistence/src/main/kotlin/com/maersk/iom/master/data/persistence/SalesObjectEntity.kt
  - diesel-price-ranges/diesel-price-ranges-persistence/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/persistence/entity/DieselPriceRangesEntity.kt
  - vessel/vessel-persistence/src/main/kotlin/com/maersk/iom/master/data/vessel/persistence/entity/VesselEntity.kt
  - vendors/vendor-persistence/src/main/kotlin/com/maersk/iom/master/data/vendor/persistence/entity/VendorDataEntity.kt
  - vendors/vendor-persistence/src/main/kotlin/com/maersk/iom/master/data/vendor/persistence/entity/VendorDataV2Entity.kt
  - vendors/vendor-persistence/src/main/kotlin/com/maersk/iom/master/data/vendor/persistence/entity/VendorsSource.kt
  - vendors/vendor-persistence/src/main/kotlin/com/maersk/iom/master/data/vendor/persistence/entity/VendorsSourceV2.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/vas/entity/VasEntity.kt
  - service/src/main/resources/db/dbchangelogmaster.xml
  - liquibase.properties
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - architecture/cross-cutting.md
  - domain/location.md
  - domain/vendors.md
---

# Database tables

- `charge_types` — `ChargeTypeEntity` columns: material_number, name, description, rail_flag, inland_flag, customs_flag, default_uom, is_non_sap_tms, is_base_charge, is_cancellation_charge, is_automatic, charge_classification, is_active, channels. (source: charges/charges-persistence/src/main/kotlin/com/maersk/iom/master/data/charges/persistance/ChargeTypeEntity.kt:7)
- `customer_to_charge_types` — `CustomerToChargeTypeEntity` columns: material_number, customer_code, country_code, is_pass_through, default_uom, is_automatic, effective_date, is_non_sap_tms, materialNumber, customerCode, countryCode. (source: charges/charges-persistence/src/main/kotlin/com/maersk/iom/master/data/charges/persistance/CustomerToChargeTypeEntity.kt:9)
- `vat_partners` — `VatPartnerEntity` columns: . (source: charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/service/vatpartner/VatPartnerEntity.kt:6)
- `country_to_vat_partners` — `CountryToVatPartnerEntity` columns: iso_country_code, default_vat_partner_code. (source: charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/service/vatpartner/CountryToVatPartnerEntity.kt:7)
- `charge_type_default_vat_partners` — `ChargeTypeDefaultVatPartnerEntity` columns: material_number, sales_office_code, country_code, vat_partner. (source: charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/service/vatpartner/ChargeTypeDefaultVatPartnerEntity.kt:7)
- `commodity_by_code` — `CommodityByCodeEntity` columns: commodityCode, commodityName, cargoTypes, groupCode, groupType, groupName, commoditySystemCd, usFlagIndicator. (source: commodity/commodity-persistence/src/main/kotlin/com/maersk/iom/master/data/commodity/persistence/CommodityByCodeEntity.kt:15)
- `commodity_by_hs_code` — `HSCommodityByCodeEntity` columns: hs_commodity_code. (source: commodity/commodity-persistence/src/main/kotlin/com/maersk/iom/master/data/commodity/persistence/HSCommodityByCodeEntity.kt:7)
- `containertypes_by_iso_code` — `ContainerTypeEntity` columns: isoContainerSizeTypeCd. (source: container/container-persistence/src/main/kotlin/com/maersk/iom/master/data/container/persistence/ContainerTypeEntity.kt:8)
- `location` — `LocationEntity` columns: geo_id, geo_type, name, rkst, rkts, unloc, country_name, country_code, country_geo_id, city_name, region_code, region_name, site_name, olson_time_zone, longitude, latitude. (source: location/location-persistence/src/main/kotlin/com/maersk/iom/master/data/location/persistence/LocationEntity.kt:10)
- `locations` — `LocationCacheEntity` columns: id, type, maersk_geo_location_id, maersk_rkst_code, maersk_rkts_code, un_loc_code, country_name, country_code, city_name, region_code, region_name, site_name, timezone_id, country_geo_id. (source: location/location-persistence/src/main/kotlin/com/maersk/iom/master/data/location/persistence/LocationCacheEntity.kt:12)
- `alternate_codes` — `AlternateCodeEntity` columns: id, geoId, code, codeType. (source: location/location-persistence/src/main/kotlin/com/maersk/iom/master/data/location/persistence/AlternateCodeEntity.kt:6)
- `alternate_names` — `AlternateNameEntity` columns: id, geo_id, name, description, status. (source: location/location-persistence/src/main/kotlin/com/maersk/iom/master/data/location/persistence/AlternateNameEntity.kt:7)
- `ifs_sales_types` — `SalesTypeEntity` columns: object_id, description. (source: sales/sales-persistence/src/main/kotlin/com/maersk/iom/master/data/persistence/SalesTypeEntity.kt:7)
- `sales_objects` — `SalesObjectEntity` columns: id, materialNumber, objectId, description, direction, zoneCode, containerSize, cityCode. (source: sales/sales-persistence/src/main/kotlin/com/maersk/iom/master/data/persistence/SalesObjectEntity.kt:6)
- `diesel_price_ranges` — `DieselPriceRangesEntity` columns: id, slab_id, min_price, max_price, country_code, customer_code, currency_code. (source: diesel-price-ranges/diesel-price-ranges-persistence/src/main/kotlin/com/maersk/iom/master/data/dieselpriceranges/persistence/entity/DieselPriceRangesEntity.kt:7)
- `vessels` — `VesselEntity` columns: vessel_code, vessel_name, type, active_status, isNewRecord. (source: vessel/vessel-persistence/src/main/kotlin/com/maersk/iom/master/data/vessel/persistence/entity/VesselEntity.kt:9)
- `vendors` — `VendorDataEntity` columns: code, name, mark_for_deletion, type, purchasing_orgs, country, city, iso_country_code, mdg_vendor_code, isNewRecord. (source: vendors/vendor-persistence/src/main/kotlin/com/maersk/iom/master/data/vendor/persistence/entity/VendorDataEntity.kt:11)
- `vendors_v2` — `VendorDataV2Entity` columns: vmdm_code, code, name, mark_for_deletion, type, purchasing_orgs, addresses, country, city, iso_country_code, mdg_vendor_code, isNewRecord. (source: vendors/vendor-persistence/src/main/kotlin/com/maersk/iom/master/data/vendor/persistence/entity/VendorDataV2Entity.kt:11)
- `vendors_source` — `VendorsSource` columns: id, code, data, created_time. (source: vendors/vendor-persistence/src/main/kotlin/com/maersk/iom/master/data/vendor/persistence/entity/VendorsSource.kt:9)
- `vendors_source_v2` — `VendorsSourceV2` columns: id, code, vmdm_code, data, created_time. (source: vendors/vendor-persistence/src/main/kotlin/com/maersk/iom/master/data/vendor/persistence/entity/VendorsSourceV2.kt:8)
- `value_added_services` — `VasEntity` columns: vas_code, description, direction, type, vas_group_enum. (source: service/src/main/kotlin/com/maersk/iom/master/data/vas/entity/VasEntity.kt:9)
