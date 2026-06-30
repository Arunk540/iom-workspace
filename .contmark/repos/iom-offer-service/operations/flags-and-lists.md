---
category: operations
title: Feature Flags and Static Lists
summary: LaunchDarkly feature flags, static allow/deny lists, and static configuration
primary_for: [feature-flag-reference, static-list-configuration-guide]
mentions: [FeatureConfigUtil, LaunchDarklyLocalConfig, ReferenceDataFetcher, MandatoryChargesConfig, AdditionalChargeMaterialCodesProperties]
scenarios:
  - find all feature flags used
  - understand empty container allow list
  - find mandatory charge code configuration
  - understand launchdarkly flag usage
  - find transport mode allow list
capabilities: [offer-search, rate-calculation]
domains: [offer-management, rates]
entities: []
sources:
  - src/main/resources/application.yml
  - src/main/kotlin/com/maersk/iom/offer/config/LaunchDarklyLocalConfig.kt
  - src/main/kotlin/com/maersk/iom/offer/config/MandatoryChargesConfig.kt
  - src/main/kotlin/com/maersk/iom/offer/config/AdditionalChargeMaterialCodesProperties.kt
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [architecture/cross-cutting.md, operations/retries.md, integrations/charge-types.md]
---

## LaunchDarkly Feature Flags

SDK key: `${launchdarklysdkkey}` / MCIM SDK key: `${launchdarklymcimsdkkey}` (source: src/main/resources/application.yml:launchdarkly)

| Flag | Method | Effect if enabled |
|---|---|---|
| Triangulation | `isTriangulationEnabled(country)` | Enables triangulation offer type in search |
| Zonal pricing | `isZonalPricingEnabled(country)` | Passes zonal pricing context to offer API |
| My Customs | `isMyCustomsIntegrationEnabled()` | Enables customs charge enrichment per plan |
| Preferred route | `isFetchOnlyPreferredRouteEnabled(country)` | Sets routing condition to `PREFERRED` |
| Estimated stay time | `features.ire.feature.estimatedstaytime.enabled` | Feature flag in `IOMRoutingAndOfferService` |

`micmEnabled: true` in application.yml controls MCIM LaunchDarkly SDK activation (source: src/main/resources/application.yml:launchdarkly.micmEnabled)

## application.yml Feature Flags

| Key | Default | Effect |
|---|---|---|
| `features.offers.feature.enabled` | `true` | Master offer feature toggle |
| `features.offers.persistence.enabled` | `false` | Whether to persist offer results |
| `features.offers.audit.enabled` | `false` | Whether to write `OfferAudit` records |
| `features.offers.mandatorychargesvalidation.enabled` | `false` | Mandatory charges pre-filter |
| `features.offers.countryweightrestrictions.enabled` | `true` | Country-level weight restriction check |
| `features.sap.tms.rules.validation.active` | `false` | SAP TMS rules validation |
| `features.countries.terminal.location` | `CI` | Countries using booking country as terminal |

(source: src/main/resources/application.yml:features)

## Allowed Transport Modes

`transport-modes: TRUCK, RAIL, BARGE, BARGE_COMBINED, RAIL_COMBINED` (source: src/main/resources/application.yml:transport-modes)

Injected into `RoutingService.allowedTransportModesEnum`; routes with transport modes outside this list are discarded (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RoutingService.kt:allowedTransportModes)

Disallowed transport mode codes: `FEF` (hard-coded default) (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RoutingService.kt:38)

## Empty Container Allow-List

`empty-container` property lists specific container numbers allowed as empty container point. `V3EmptyContainerValidator` enforces this list. (source: src/main/resources/application.yml:empty-container)

## Additional Charge Material Codes

Maps `AdditionalChargeType` enum values to material codes (source: src/main/resources/application.yml:additional-charge-material-codes):
- `WAITING_CHARGE` → `10001`
- `CHASSIS_RENTALS` → `10002`
- `STORAGE_TIME` → `10003`
- `FUEL_SURCHARGE` → `10004`

## Mandatory Charge Codes

See `integrations/charge-types.md` for the full configured code list per direction and transport mode.
