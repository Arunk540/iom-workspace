---
category: architecture
title: Cross-cutting concerns
summary: Security, exception handling, retries, tracing, feature flags, and cache behavior are centralized in config, controller advice, and webclient utilities.
primary_for: [cross-cutting-map]
mentions: [security, errors, tracing, retries, cache]
scenarios:
  - cross cutting map
  - cross cutting security
  - cross cutting errors
  - tracing setup
  - retry behavior
capabilities: [architecture]
domains: [Web Integrator]
entities: [SecurityConfig, WebIntegratorExceptionHandler, RetryConfiguration]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/config/SecurityConfig.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/exception/WebIntegratorExceptionHandler.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/ErrorHandler.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - operations/retries.md
  - operations/monitoring.md
---
# Cross-cutting concerns

- Security is WebFlux-native: actuator and Swagger are `permitAll`, everything else requires authentication via `oauth2ResourceServer().jwt(...)`. (source: src/main/kotlin/com/maersk/iom/webintegrator/config/SecurityConfig.kt:89)
- JWT decoding is issuer-aware; tokens are routed to ForgeRock or Azure decoders after parsing the `iss` claim. (source: src/main/kotlin/com/maersk/iom/webintegrator/config/SecurityConfig.kt:162)
- Method-level authorization is used for soft close via `@PreAuthorize("hasAnyRole(@OrderController.softClosureUpdatePermittedRoles)")`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:211)
- Global exception mapping converts validation, partial-content, partial-update, forbidden, and external-api errors into JSON payloads. (source: src/main/kotlin/com/maersk/iom/webintegrator/exception/WebIntegratorExceptionHandler.kt:50)
- Outbound retries are centralized in `ErrorHandler.configureRetry`, limited to 5xx and HTTP 409 with backoff + jitter. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/ErrorHandler.kt:56)
- Micrometer observation is attached to every WebClient, with a custom URI tag convention to preserve outbound path labels. (source: src/main/kotlin/com/maersk/iom/webintegrator/config/WebClientConfiguration.kt:225)
- Feature flags are sourced from LaunchDarkly; local profile can read `FeatureCatalog.json` instead of the live SDK. (source: src/main/kotlin/com/maersk/iom/webintegrator/config/LaunchDarklyLocalConfig.kt:23)
- Infinispan remote caching is optional and only enabled when `spring.cache.type=INFINISPAN`; application.yml defaults cache type to `NONE`. (source: src/main/kotlin/com/maersk/iom/webintegrator/config/RemoteCacheConfig.kt:17)
