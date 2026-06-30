---
category: architecture
title: Modules and package layout
summary: The source tree is a conventional layered WebFlux service with controllers, services, mappers, webclients, config, and DTO/model packages.
primary_for: [module-layout]
mentions: [packages, modules, tree]
scenarios:
  - package tree map
  - module layout
  - where packages live
  - understand source tree
  - browse repo modules
capabilities: [architecture]
domains: [Web Integrator]
entities: [controller, service, mapper]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator
  - src/main/resources
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - architecture/cross-cutting.md
  - stack/stack.md
---
# Modules and package layout

- `config/` — security, WebClient beans, LaunchDarkly local bootstrap, cache, Swagger, and typed properties. (source: src/main/kotlin/com/maersk/iom/webintegrator/config/SecurityConfig.kt:57)
- `controller/` — all inbound REST handlers for bookings, offers, orders, preferences, rules, documents, incidents, notification, invoicing, and CCD search. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:27)
- `service/` — orchestration layer for booking creation, offer search, order lifecycle, business rules, CCD, feature flags, and external health checks. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OrderService.kt:32)
- `mapper/` and `mapper/billing/` — MapStruct and extension-function mapping between generated downstream models and BFF/OpenAPI models. (source: src/main/kotlin/com/maersk/iom/webintegrator/mapper/V3ServicePlanMapper.kt:26)
- `model/dto/` and `model/dto/booking/` — internal DTOs for booking bootstrap, decisioning, CCD, export documents, and ocean-booking enrichment. (source: src/main/kotlin/com/maersk/iom/webintegrator/model/dto/booking/Booking.kt:8)
- `webclient/` and `webclient/decisioningservice/` — non-blocking downstream integrations. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/OrderClient.kt:26)
- `validator/` — haulage validation helpers used during create/amend order flows. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OrderService.kt:593)
- `exception/` — controller advice and typed error classes. (source: src/main/kotlin/com/maersk/iom/webintegrator/exception/WebIntegratorExceptionHandler.kt:22)
- `utils/` — JWT token parsing helpers. (source: src/main/kotlin/com/maersk/iom/webintegrator/utils/JwtTokenUtils.kt:6)
- `resources/openapi/` — source-of-truth OpenAPI contracts for web-integrator, offer, order, billing, and agent-portal integrations. (source: build.gradle.kts:132)
- `resources/data/` — static optional document/reference and party-role lists used by business-rule assembly. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/BusinessRuleService.kt:21)
