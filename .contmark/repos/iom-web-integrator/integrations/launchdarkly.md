---
category: integrations
title: LaunchDarkly integration
summary: LaunchDarkly controls config-rules responses, notification banners, and feature gates such as haulage validation or container soft deletion.
primary_for: [launchdarkly-integration]
mentions: [launchdarkly, feature flags, notification banner, config rules]
scenarios:
  - launchdarkly integration
  - launchdarkly flags
  - feature flag integration
  - notification flag source
  - config rules flag source
capabilities: [integration-launchdarkly]
domains: [Operations]
entities: [FeatureFlagProvider, LaunchDarklyFeatureProvider]
sources:
  - src/main/resources/application.yml
  - src/main/kotlin/com/maersk/iom/webintegrator/controller/ConfigController.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/FeatureFlagProvider.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/config/LaunchDarklyLocalConfig.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - operations/flags-and-lists.md
  - runtime/notification-health-flow.md
---
# LaunchDarkly integration

- `ConfigController` calls `LaunchDarklyFeatureProvider.getJsonValueBasedOnCustomerAndCountry(...)` with flag `booking_be-MCI-rollout-plan-config`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/ConfigController.kt:16)
- `FeatureFlagProvider` wraps boolean flags for haulage validation and container soft deletion, keeping the rest of the service layer off the raw SDK. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/FeatureFlagProvider.kt:13)
- Notification banner retrieval is mediated by `FeatureConfigUtil.getNotificationBanner()` after health checks pass. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/NotificationController.kt:50)
- Local profile uses `FeatureCatalog.json` via `FileData.dataSource().classpathResources(...)` instead of live streaming. (source: src/main/kotlin/com/maersk/iom/webintegrator/config/LaunchDarklyLocalConfig.kt:23)
