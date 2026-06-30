---category: architecture
title: Modules and package tree
summary: The repository is a single Spring Boot WebFlux service organized by ingress, domain orchestration, queries, mapping, incident support, and cross-cutting config. Kotlin source under `com.maersk.iom` is compact and service-centric.
primary_for: [module-topology]
mentions: [package-tree, controllers, messaging, domain, services]
scenarios:
  - inspect package tree
  - find module boundaries
  - map source layout
  - locate domain packages
  - locate resource assets
  - understand module layout
capabilities: [architecture]
domains: [order]
entities: [ServicePlan, Booking, Incident]
sources:
  - src/main/kotlin/com/maersk/iom/IomOrderServiceApplication.kt
  - src/main/kotlin/com/maersk/iom/AGENTS.md
  - src/main/resources/application.yaml
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - architecture/cross-cutting.md
  - stack/stack.md
  - navigation/key-classes.md
---
```text
com/maersk/iom
├── config
├── controller
│   ├── v2
│   └── v3
├── entity
│   └── v2
├── filter
├── incident
│   ├── controller
│   ├── enums
│   ├── mapper
│   ├── model
│   └── service
├── mapper
├── messaging
├── model
│   └── event
│       ├── handler
│       └── strategy
├── order
│   └── domain
│       ├── config
│       └── models
├── scheduler
├── service
│   └── audit
└── validator
```

## Resource modules
- `src/main/resources/application*.y*ml` holds runtime configuration, topic names, endpoints, roles, flags, and R2DBC/Liquibase wiring.
- `src/main/resources/avro` defines Booking, ServicePlan, and ServicePlanDomainEvent schemas used by generated Kafka payload classes.
- `src/main/resources/db` stores Liquibase changelogs.
- `src/main/resources/jolt` stores customer-history transformation rules.
- `src/main/resources/spec` stores the bundled API YAML.
