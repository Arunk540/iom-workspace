---
category: operations
title: Flags, lists, and static config
summary: Runtime behavior is influenced by LaunchDarkly flags, Spring properties, timezone maps, mandatory charge IDs, and static JSON lookup lists.
primary_for: [flags-and-lists]
mentions: [feature flags, lists, timezones, mandatory charges, roles]
scenarios:
  - feature flags list
  - static lists map
  - timezone map
  - mandatory charges list
  - role property list
capabilities: [operations-config-flags]
domains: [Operations]
entities: [FeatureFlagProvider, MandatoryChargesProperties, TimezoneProperties]
sources:
  - src/main/resources/application.yml
  - src/main/kotlin/com/maersk/iom/webintegrator/service/FeatureFlagProvider.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/BusinessRuleService.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - integrations/launchdarkly.md
  - domain/business-rules.md
---
# Flags, lists, and static config

- `FeatureFlagProvider` exposes `iom-wi-haulage-validator-enabled` and `iom-wi-container-soft-deletion-enabled`. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/FeatureFlagProvider.kt:13)
- `ConfigController` reads LaunchDarkly JSON flag `booking_be-MCI-rollout-plan-config` for customer/country-specific config rules. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/ConfigController.kt:16)
- Spring properties include `softClosureUpdatePermittedRoles` and `priceOwnersForPriceVariation`, both consumed directly by controllers. (source: src/main/resources/application.yml:25)
- `timezones.countryTimeZoneMap` provides fallback timezone IDs for countries such as KE, UG, AE, PH, JP, PK, TR, VN, ID, and DK. (source: src/main/resources/application.yml:271)
- `mandatory-charges.import.customs` and `mandatory-charges.export.customs` hold the customs charge-type IDs used for customs-price availability checks. (source: src/main/resources/application.yml:284)
- Business-rule optional lists come from `OptionalDocument.json`, `OptionalReference.json`, and `PartyMasterRoles.json`. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/BusinessRuleService.kt:21)
