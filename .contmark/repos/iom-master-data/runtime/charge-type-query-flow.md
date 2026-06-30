---
category: runtime
title: Charge type enrichment flow
summary: How charge-type requests layer repository filters, customer overrides, UOM enrichment, and VAT partner defaults.
primary_for: [charge-query-flow]
mentions: [charges, uom, vat, customer-mapping, feature-flags]
scenarios:
  - trace charge request
  - see uom enrichment
  - find vat default
  - inspect customer overrides
  - follow charge filters
capabilities: [runtime-flow]
domains: [charges]
entities: [ChargesController, ChargeTypeServiceImpl, VatPartnerServiceImpl]
sources:
  - charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/ChargesController.kt
  - charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/service/ChargeTypeServiceImpl.kt
  - charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/service/vatpartner/VatPartnerServiceImpl.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - domain/charges.md
  - contracts/db-schemas.md
  - navigation/scenarios.md
---

# Flow

1. `GET /charge-types` binds a validated `ChargeTypeRequest` and forwards it to `ChargeTypeServiceImpl.getChargeTypes`. (source: charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/ChargesController.kt:28)
2. The service resolves SAP/TMS feature state, reads all base charge types, and filters by automatic flag, cancellation flag, active state, channel, material numbers, and search text. (source: charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/service/ChargeTypeServiceImpl.kt:26)
3. If customer and country are present, `customer_to_charge_types` rows override default UOM, automatic behavior, non-SAP/TMS flag, and pass-through flag for matching material numbers. (source: charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/service/ChargeTypeServiceImpl.kt:75)
4. Every surviving charge is enriched through `UomDataService`, and missing UOM metadata raises `DataEnrichmentError`. (source: charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/service/ChargeTypeServiceImpl.kt:123)
5. The flow finally consults `ChargeTypeDefaultVatPartnerLoadService` and overlays a default VAT partner when a sales-office/country mapping exists. (source: charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/service/ChargeTypeServiceImpl.kt:58)
