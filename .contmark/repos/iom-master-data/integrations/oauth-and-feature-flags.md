---
category: integrations
title: OAuth providers and feature flags
summary: Cross-cutting external dependencies include ForgeRock/Azure AD OAuth providers and LaunchDarkly for local feature-flag development.
primary_for: [oauth-and-flags-map]
mentions: [forgerock, azuread, launchdarkly, oauth2, feature-flags]
scenarios:
  - find oauth provider
  - inspect feature flags
  - see token endpoints
  - find local ld config
  - trace client credentials
capabilities: [integration-map]
domains: [platform]
entities: [LaunchDarklyLocalConfig, CommodityWebClientConfiguration, WebClientConfiguration]
sources:
  - service/src/main/resources/application.yaml
  - service/src/main/kotlin/com/maersk/iom/master/data/config/LaunchDarklyLocalConfig.kt
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/webclient/configuration/CommodityWebClientConfiguration.kt
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/configuration/WebClientConfiguration.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - operations/monitoring.md
  - integrations/business-rules-service.md
  - integrations/upstream-location-and-facility.md
---

# Systems

- `iomAuthProvider` uses ForgeRock `${forgerockTokenUri}` credentials for commodity restriction calls, while `facilityAuthProvider` uses Azure AD `${azureAdTokenUri}` for facility lookups. (source: service/src/main/resources/application.yaml:57)
- `LaunchDarklyLocalConfig` only creates an `LDReactorClient` in the `local` profile and reads flags from `FeatureCatalog.json` instead of the hosted LaunchDarkly stream. (source: service/src/main/kotlin/com/maersk/iom/master/data/config/LaunchDarklyLocalConfig.kt:23)
- The service also exposes `launchdarkly.sdkKey` and `launchdarkly.mcimSdkKey` properties in `application.yaml`, showing that feature-flag SDK credentials are part of runtime configuration. (source: service/src/main/resources/application.yaml:11)
