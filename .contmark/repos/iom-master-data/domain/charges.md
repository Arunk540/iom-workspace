---
category: domain
title: Charge types and VAT partners
summary: Charges serves charge type reference data with customer/UOM/VAT enrichment and separate VAT partner query APIs.
primary_for: [charge-types-management]
mentions: [charge-types, vat-partners, uom, customer-mapping]
scenarios:
  - trace charge query
  - find vat partner logic
  - see charge tables
  - inspect uom enrichment
  - find default vat partner
capabilities: [domain-summary]
domains: [charges]
entities: [ChargesController, ChargeTypeServiceImpl, VatPartnerController, VatPartnerServiceImpl]
sources:
  - charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/ChargesController.kt
  - charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/VatPartnerController.kt
  - charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/service/ChargeTypeServiceImpl.kt
  - charges/charges-controller/src/main/kotlin/com/maersk/iom/master/data/charges/controller/service/vatpartner/VatPartnerServiceImpl.kt
  - charges/charges-persistence/src/main/kotlin/com/maersk/iom/master/data/charges/persistance/ChargeTypeEntity.kt
  - charges/charges-persistence/src/main/kotlin/com/maersk/iom/master/data/charges/persistance/CustomerToChargeTypeEntity.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - runtime/charge-type-query-flow.md
  - contracts/db-schemas.md
  - navigation/scenarios.md
---

# Scope

- `ChargesController` provides charge-type queries; `VatPartnerController` handles VAT-partner retrieval.
- Startup loaders keep VAT partner and default VAT partner mappings ready in memory.
- Charge queries enrich repository data with customer mappings, UOM details, feature flags, and default VAT partner resolution.

## Data model

- `charge_types` stores the base master list.
- `customer_to_charge_types` narrows availability and overrides UOM/automation attributes for customer context.
- VAT partner data lives in `vat_partners`, `country_to_vat_partners`, and `charge_type_default_vat_partners`.
