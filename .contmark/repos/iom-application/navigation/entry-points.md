---
category: navigation
title: Library entry points
summary: Maps the public APIs exposed by each Gradle module so agents can start from the right interface instead of drilling into implementation code.
primary_for: [library-entry-points]
mentions: [orderservice, orderrepository, resolver, webclient, validator]
scenarios:
  - find public api
  - where to start
  - library surface map
  - module entry point
  - shared jar exports
capabilities: [entry-point-routing]
domains: [iom-application]
entities: [OrderService, OrderRepository, Resolver, FeatureProvider]
sources:
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/OrderService.kt
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/OrderRepository.kt
  - application-validators/src/main/kotlin/com/maersk/iom/validator/IOMValidator.kt
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/config/WebClientConfiguration.kt
  - resolvers/src/main/kotlin/com/maersk/iom/resolvers/Resolver.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - navigation/scenarios.md
  - navigation/key-classes.md
  - architecture/modules.md
---
## What this repository provides

This repo is a publishable Kotlin library set, not an HTTP app. The root build disables `bootJar`, disables the root `test` task, and publishes an `application` Java component, while submodules publish their own jars with sources and javadocs.

## Module entry points

| Module | Start here | What it exposes |
|---|---|---|
| `application-model` | `V1ServicePlanModel`, `V2RoutingRequestModel`, `V3ServicePlanModel` | Versioned request/response DTOs plus custom validation annotations for the API surface. |
| `application-validators` | `IOMValidator`, `IOMBasicValidator`, `IOMBusinessRuleValidator`, `IomSapTmsRulesValidator` | Stateless validation contracts for structural rules, business rules, and SAP/TMS amendment checks. |
| `iom-common` | `FeatureProvider`, `RequestContextProvider`, `IOMEnums` | Shared flags, enums, request context, converters, and utility types used by every other module. |
| `iom-order-domain` | `OrderService`, `OrderRepository`, `BusinessExceptionRepository`, `ServicePlan` | Hexagonal domain ports and the aggregate model for create, amend, cancel, execute, and status flows. |
| `iom-persistence` | `OrderRepositoryImpl`, `ServicePlanEntityRepository`, `ServicePlanMapper` | R2DBC + Mongo adapter layer that fulfills the domain repository contract. |
| `reference-cache` | `ReferenceDataFetcher` | Startup and scheduled warm caches for high-volume master/reference lookups. |
| `reference-data-client` | `WebClientConfiguration` and `*Client` interfaces | OAuth2-enabled reactive clients for customer, facility, routing, offer, Telikos, business-rules, and notification systems. |
| `resolvers` | `Resolver<T>` and concrete `*Resolver` classes | Enrichment pipeline that turns IDs and codes into resolved external data before validation/domain logic. |

## Best first files by concern

- Order lifecycle and mutations: `iom-order-domain/.../OrderService.kt`
- DB persistence behavior: `iom-persistence/.../OrderRepositoryImpl.kt`
- External master-data lookups: `reference-data-client/.../config/WebClientConfiguration.kt`
- Cache-backed enrichment: `reference-cache/.../ReferenceDataFetcher.kt`
- Validation orchestration: `application-validators/.../IOMValidator.kt`
- Pre-domain enrichment: `resolvers/.../Resolver.kt`
