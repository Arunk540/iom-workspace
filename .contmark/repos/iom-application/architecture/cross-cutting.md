---
category: architecture
title: Cross-cutting architecture
summary: Highlights the shared patterns that span modules: reactive APIs, versioned contracts, dual persistence, caching, and feature gating.
primary_for: [cross-cutting-architecture]
mentions: [reactive stack, versioned contracts, dual persistence, feature flags, caching]
scenarios:
  - shared architecture patterns
  - cross cutting concerns
  - reactive design overview
  - where caching fits
  - how versions upgrade
capabilities: [architecture-overview]
domains: [iom-application]
entities: [ServicePlan, ServicePlanEntity, ReferenceDataFetcher]
sources:
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/OrderService.kt
  - iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/entity/ServicePlanEntity.kt
  - reference-cache/src/main/kotlin/com/maersk/iom/config/ReferenceDataFetcher.kt
  - iom-common/src/main/kotlin/com/maersk/iom/common/features/LaunchDarklyFeatureProvider.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - architecture/modules.md
  - runtime/order-processing.md
  - operations/flags-and-lists.md
---
## Shared patterns

- **Reactive everywhere**: domain ports, repositories, clients, validators, and resolvers expose `Mono`/`Flux` instead of blocking APIs.
- **Versioned API contracts**: `application-model` keeps parallel `v1`, `v2`, and `v3` DTO packages while domain types live separately in `iom-order-domain`.
- **Dual persistence representation**: PostgreSQL metadata and JSON payloads live in `ServicePlanEntity`, while a Mongo `ServicePlanDocument` mirrors the richer aggregate shape.
- **Upgrade-on-read compatibility**: older serialized payload versions are transformed to the latest domain version when loaded.
- **Reference data as a platform service**: external masters are centralized behind WebClient wrappers and optionally fronted by startup/scheduled caches.
- **Feature-gated behavior**: LaunchDarkly-backed configuration and boolean flags let downstream services vary behavior by customer/country.
- **Event/audit support**: the domain can materialize change events while persistence owns outbox, audit, incident, and customer-history records.

## Why this repo behaves like a shared library

- It publishes module jars rather than an executable boot application.
- It packages reusable DTOs, validators, domain models, persistence adapters, reference clients, and resolvers in one dependency set.
- Downstream services can adopt just the modules they need: contract-only, domain-only, persistence-enabled, or enrichment-enabled.
