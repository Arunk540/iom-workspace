---
category: operations
title: Flags and controlled lists
summary: Summarizes the feature-flag surface and the major controlled enumerations that shape validation and runtime decisions.
primary_for: [flags-and-controlled-lists]
mentions: [launchdarkly, feature catalog, enums, controlled vocabulary]
scenarios:
  - find feature flags
  - which enums exist
  - controlled values list
  - launchdarkly config
  - country flag behavior
capabilities: [flag-analysis]
domains: [operations]
entities: [FeatureCatalog, LaunchDarklyFeatureProvider, LocationFunctionEnum]
sources:
  - iom-common/src/main/kotlin/com/maersk/iom/common/features/FeatureCatalog.kt
  - iom-common/src/main/kotlin/com/maersk/iom/common/features/FeatureProvider.kt
  - iom-common/src/main/kotlin/com/maersk/iom/common/features/LaunchDarklyFeatureProvider.kt
  - iom-common/src/main/kotlin/com/maersk/iom/common/enums/IOMEnums.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - operations/failure-model.md
  - domain/party.md
---
## Feature flags

- `FeatureCatalog` currently exposes top-level boolean flags for multi-stop, single-click migration to execution, and my-customs integration. (source: iom-common/src/main/kotlin/com/maersk/iom/common/features/FeatureCatalog.kt:6)
- `FeatureProvider` is the shared abstraction for reactive flag lookup, returning a `FeatureResult` for a typed feature flag. (source: iom-common/src/main/kotlin/com/maersk/iom/common/features/FeatureProvider.kt:5)
- `LaunchDarklyFeatureProvider` evaluates boolean/string/int flags, supports JSON-valued rollout config, and aggregates a larger MICM country-level set that includes pricing, single-click confirm, DG, triangulation, SAP/TMS, SCM, S4 Hana, customs, invoicing, bulk amend, vessel-ETD-based PCD, and customer-history scheduler flags. (source: iom-common/src/main/kotlin/com/maersk/iom/common/features/LaunchDarklyFeatureProvider.kt:34) (source: iom-common/src/main/kotlin/com/maersk/iom/common/features/LaunchDarklyFeatureProvider.kt:74)
- MICM country targeting uses a LaunchDarkly context of kind `booking` with `booking.country`, rather than a generic user context. (source: iom-common/src/main/kotlin/com/maersk/iom/common/features/LaunchDarklyFeatureProvider.kt:270)

## Controlled lists

- `IOMEnums.kt` is the central list file for core vocabularies such as `LocationFunctionEnum`, `CustomsStatusEnum`, `PartyMasterRoleEnum`, `ProductEnum`, `TelecommunicationNumberTypeEnum`, `TransportAssetRequirementTypeEnum`, `TemperatureUnit`, and event-trigger enums. (source: iom-common/src/main/kotlin/com/maersk/iom/common/enums/IOMEnums.kt:3)
- The location-function list spans inland, ocean, terminal, warehouse, and pickup/drop concepts, which is why service-plan legs and validators rely on enum names rather than free text. (source: iom-common/src/main/kotlin/com/maersk/iom/common/enums/IOMEnums.kt:10)
- Party and product lists are especially important for resolver and booking logic because they define roles like `BOOKED_BY`, `PRICE_OWNER`, and product families like `IMPORT_INTERMODAL_MULTICARRIER` and `INTERMODAL_SINGLECARRIER`. (source: iom-common/src/main/kotlin/com/maersk/iom/common/enums/IOMEnums.kt:87) (source: iom-common/src/main/kotlin/com/maersk/iom/common/enums/IOMEnums.kt:180)
