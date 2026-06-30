---category: architecture
title: Cross-Cutting Concerns
summary: Security, observability, error handling, retry, and feature flags
primary_for: [cross-cutting-concern-catalog, security-observability-reference]
mentions: [SecurityConfig, ObservationWebFilter, ObservationHandlerConfig, ObservedAspectConfiguration, WebClientConfiguration, ErrorHandler, ReferenceDataFetcher, FeatureConfigUtil, LaunchDarklyLocalConfig]
scenarios:
  - understand jwt token validation
  - trace request through observability stack
  - understand retry and circuit breaker config
  - find feature flag checks
  - understand error propagation model
  - find cross-cutting concerns
  - check security concerns
capabilities: [jwt-auth, distributed-tracing, circuit-breaking, retry, feature-flags]
domains: [offer-management, security, observability]
entities: []
sources:
  - src/main/kotlin/com/maersk/iom/offer/config/SecurityConfig.kt
  - src/main/kotlin/com/maersk/iom/offer/filter/ObservationWebFilter.kt
  - src/main/kotlin/com/maersk/iom/offer/config/ObservationHandlerConfig.kt
  - src/main/kotlin/com/maersk/iom/offer/config/ObservedAspectConfiguration.kt
  - src/main/kotlin/com/maersk/iom/offer/webclient/WebClientConfiguration.kt
  - src/main/kotlin/com/maersk/iom/offer/webclient/ErrorHandler.kt
  - src/main/kotlin/com/maersk/iom/offer/config/ReferenceDataFetcher.kt
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [architecture/modules.md, operations/retries.md, operations/monitoring.md, operations/failure-model.md]
---

## Security

**Class**: `SecurityConfig` — `@EnableWebFluxSecurity`, `@EnableReactiveMethodSecurity` (source: src/main/kotlin/com/maersk/iom/offer/config/SecurityConfig.kt:34)

### Dual JWT Issuer Support
- **Forgerock** (issuer contains `maersk.com`): uses `forgeRockJwtDecoder` bean; JWKS from ForgeRock endpoint (source: src/main/kotlin/com/maersk/iom/offer/config/SecurityConfig.kt:forgeRockJwtDecoder)
- **Azure AD**: uses `auzreADJwtDecoder` bean; JWKS from Azure login endpoint (source: src/main/kotlin/com/maersk/iom/offer/config/SecurityConfig.kt:auzreADJwtDecoder)
- Token is parsed first to extract `iss` claim, then routed to correct decoder (source: src/main/kotlin/com/maersk/iom/offer/config/SecurityConfig.kt:reactiveJwtDecoder)

### Role Extraction
`GrantedAuthoritiesExtractor` reads `roles` claim; falls back to `scope`/`scp` claims. Adds `ROLE_API_SCOPE` for non-Maersk-domain tokens with API scope (source: src/main/kotlin/com/maersk/iom/offer/config/SecurityConfig.kt:GrantedAuthoritiesExtractor)

### Public Paths
- `GET /actuator/**` — no auth
- `/v3/api-docs/**`, `/webjars/swagger-ui/**`, `/swagger-ui.html` — no auth

## Observability

**Filter**: `ObservationWebFilter` — adds Micrometer observation spans for each HTTP request (source: src/main/kotlin/com/maersk/iom/offer/filter/ObservationWebFilter.kt)

**Handler**: `ObservationHandlerConfig` — registers Micrometer observation handlers (source: src/main/kotlin/com/maersk/iom/offer/config/ObservationHandlerConfig.kt)

**Aspect**: `ObservedAspectConfiguration` — enables `@Observed` on service beans; `ServicePlanDomainEventConsumer` uses `@Observed(name="ServicePlanDomainEventConsumer")` (source: src/main/kotlin/com/maersk/iom/offer/messaging/ServicePlanDomainEventConsumer.kt:17)

**Tracing**: W3C propagation; sampling probability 1.0; Zipkin endpoint via `${zipkinbaseurl}` (source: src/main/resources/application.yml:management.tracing)

**Metrics**: Prometheus via `/actuator/prometheus`; percentile histograms for `http.server.requests` and `http.client.requests` at p90/p95/p99 (source: src/main/resources/application.yml:management.metrics)

## Retry & Circuit Breaker

All WebClients use `ErrorHandler.configureRetry` with shared `RetryConfiguration` (source: src/main/kotlin/com/maersk/iom/offer/webclient/ErrorHandler.kt:configureRetry):
- Default: count=3, minBackOff=2s, jitter=0.75
- Retries only on `ExtendedExternalApiException` unless status is 422 Unprocessable Entity

Resilience4j circuit breakers per named instance (e.g., `offerClientInstance`, `ratesClientInstance`, `routingClientInstance`). Default config: sliding window 6, failure threshold 50%, wait 30s (source: src/main/resources/application.yml:resilience4j.circuitbreaker)

## Feature Flags (LaunchDarkly)

`FeatureConfigUtil` wraps LaunchDarkly SDK. Used to check:
- `isTriangulationEnabled(country)` — controls triangulation offer type in search flow (source: src/main/kotlin/com/maersk/iom/offer/service/v3/IOMRoutingAndOfferService.kt:searchForServicePlans)
- `isZonalPricingEnabled(country)` — controls zonal pricing pass-through
- `isMyCustomsIntegrationEnabled()` — guards customs charges enrichment
- `isFetchOnlyPreferredRouteEnabled(country)` — routing condition `PREFERRED` vs `ANY`

`LaunchDarklyLocalConfig` provides fallback SDK for non-production use (source: src/main/kotlin/com/maersk/iom/offer/config/LaunchDarklyLocalConfig.kt)

## Reference Data Cache

`ReferenceDataFetcher` loads charge type names, commodity data, container types at startup and caches in-memory. Refreshed on configured cron `0 0 1 * * ?` (source: src/main/resources/application.yml:api.charge.cron). Used by `CustomsService.getChargeTypeName` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/CustomsService.kt:getChargeTypeName)

## WebClient Configuration

`WebClientConfiguration` creates named `WebClient` beans for each external system, each with:
- `connect-timeout-ms: 5000`, `read-timeout-ms: 24000` (source: src/main/resources/application.yml:services.external)
- OAuth2 `client_credentials` grant via respective Spring Security registration
