---
category: runtime
title: Notification and health flow
summary: The notification endpoint first checks downstream health across configured services and only returns the feature-banner JSON when every required dependency is up.
primary_for: [notification-health-flow]
mentions: [notification banner, health check, outage banner, service down]
scenarios:
  - notification flow
  - health banner flow
  - service outage banner
  - external health check
  - notification endpoint debug
capabilities: [notification-health]
domains: [Operations]
entities: [ServiceHealthResponse, HealthStatus]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/controller/NotificationController.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/ExternalServiceHealthService.kt
  - src/main/resources/application.yml
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - integrations/launchdarkly.md
  - operations/monitoring.md
---
# Notification and health flow

- `NotificationController.checkExternalServices()` starts by calling `ExternalServiceHealthService.checkAllServices()`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/NotificationController.kt:44)
- Health targets come from `external-services.serviceURLMap` in `application.yml`, currently including order, offer, billing, and master-data health URLs. (source: src/main/resources/application.yml:28)
- Each service health check appends `/actuator/health`, applies a 5 second timeout, and records response time plus any error message. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/ExternalServiceHealthService.kt:70)
- Only when all services are `UP` does the controller fetch the notification banner feature JSON from `FeatureConfigUtil`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/NotificationController.kt:47)
- Any failed health probe returns a 503 body with the failed service summary; unexpected controller errors return 500. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/NotificationController.kt:61)
