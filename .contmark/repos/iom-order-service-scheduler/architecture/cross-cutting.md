---category: architecture
title: Cross-cutting concerns
summary: Shared concerns span all schedulers and outbound clients: WebFlux security, feature gating, observability, Reactive/blocked execution, and configuration-first integration wiring.
primary_for: [cross-cutting-concerns]
mentions: [security-filtering, observability-wiring, feature-flag-gating]
scenarios:
  - auth setup
  - tracing setup
  - feature flag wiring
  - shared config
  - blocking in schedulers
  - find cross-cutting concerns
  - check security concerns
capabilities: [architecture]
domains: [IOM]
entities: [LDReactorClient, SecurityWebFilterChain, WorkflowClient]
sources:
  - src/main/kotlin/com/maersk/iom/config/SecurityConfig.kt
  - src/main/kotlin/com/maersk/iom/config/LaunchDarklyLocalConfig.kt
  - src/main/kotlin/com/maersk/iom/offer/WebClientConfiguration.kt
  - src/main/kotlin/com/maersk/iom/IomOrderSchedulerApplication.kt
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - stack/stack.md
  - operations/monitoring.md
---

# Cross-cutting concerns

- **Security**: `SecurityConfig` permits `GET /actuator/**` and requires authentication for every other exchange, even though the repo exposes no traced controllers. This matters for actuator scraping and future endpoints.
- **Scheduler blocking model**: scheduled methods intentionally terminate reactive chains with `block()` or `blockLast()`, so work completes on Spring scheduler threads before the next tick.
- **Feature gating**: outbox producers short-circuit when `kafka.service-plan.active`, `kafka.service-plan-domain-event.active`, or `kafka.external-event.booking.active` are false; repricing and offer cleanup are also property-gated.
- **Observability**: Micrometer observation wraps producer chains, `logElapsedTime` surrounds several schedulers/clients, and logback writes JSON with trace/span IDs.
- **OAuth/WebClient reuse**: all outbound HTTP clients are created by `WebClientConfiguration` with shared timeout, Akamai `X-Requestor`, logging filters, and client-credentials filters.
- **Temporal enablement**: `TemporalConfig` only loads when `temporal.enabled=true`; the same service both creates workflow stubs and hosts the worker classes configured in `spring.temporal.workers`.
- **Local-only LaunchDarkly**: `LaunchDarklyLocalConfig` activates under `local` profile and reads `FeatureCatalog.json` instead of remote flag streaming.
- **Context propagation**: `main()` enables Reactor automatic context propagation before bootstrapping Spring.
- **Mongo disabled in app bootstrap**: `IomOrderSchedulerApplication` excludes reactive Mongo auto-config even though `application-local.yaml` still contains Mongo properties.
