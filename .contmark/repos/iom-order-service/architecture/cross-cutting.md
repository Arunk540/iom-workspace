---category: architecture
title: Cross-cutting concerns
summary: Security, tracing, OpenAPI grouping, local LaunchDarkly wiring, and mapper defaults are centralized under `config/` and `filter/`. Domain and service layers raise validation and access exceptions instead of hiding failures inside controllers.
primary_for: [cross-cutting-behavior]
mentions: [security, tracing, swagger, launchdarkly, exceptions]
scenarios:
  - inspect jwt setup
  - inspect observability
  - inspect feature flags
  - inspect swagger groups
  - inspect error sources
  - understand cross-cutting behavior
  - check shared behavior
capabilities: [architecture]
domains: [platform]
entities: [Jwt, Observation, LDReactorClient, ValidationException]
sources:
  - src/main/kotlin/com/maersk/iom/config/SecurityConfig.kt
  - src/main/kotlin/com/maersk/iom/filter/ObservationWebFilter.kt
  - src/main/kotlin/com/maersk/iom/config/ObservationHandlerConfig.kt
  - src/main/kotlin/com/maersk/iom/config/LaunchDarklyLocalConfig.kt
  - src/main/kotlin/com/maersk/iom/config/SwaggerConfig.kt
  - src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - operations/monitoring.md
  - operations/failure-model.md
  - stack/stack.md
---
## Security
- `SecurityConfig` authenticates every exchange except actuator and Swagger paths, extracts roles/scopes from JWT, and switches decoder selection between ForgeRock and Azure issuer families.
- `DefaultProfile` can disable reactive JWT validators entirely when `spring.application.reactive-jwt-validators.enabled=false`.

## Observability
- `ObservationWebFilter` adds `Trace-Id` response headers and wraps WebFlux exchanges in Micrometer observation.
- `ObservedAspectConfiguration` enables `@Observed`; `ObservationHandlerConfig` copies trace/span IDs into MDC.

## Feature flags
- `LaunchDarklyLocalConfig` loads local flags from `FeatureCatalog.json` under the `local` profile.
- Domain and messaging flows call feature utilities for single-click confirm, bulk amend, vessel-date repricing, lazy transform, and customer-history scheduler gating.

## API description
- `SwaggerConfig` creates OpenAPI metadata and grouped docs for `v2` and `v3` packages.

## Error style
- Domain/services throw `ValidationException`, `NotFoundException`, `ForbiddenException`, `AccessDeniedException`, and `OptimisticLockException`; controllers mostly let these propagate after building request context.
