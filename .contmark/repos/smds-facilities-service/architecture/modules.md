---category: architecture
title: Module layout
summary: The repo is split into a main `service/` Maven module, a separate `componenttest/` module, database scripts, and deployment assets.
primary_for: [facilities-module-layout]
mentions: [service, componenttest, postgresql_scripts, helm, infrastructure, spec]
scenarios:
  - inspect package tree
  - locate source module
  - find component tests
  - find database scripts
  - find deployment assets
  - understand module layout
capabilities: [module-mapping]
domains: [smds-facilities]
entities: [service, componenttest, postgresql_scripts, helm, infrastructure, spec]
sources:
  - service/pom.xml
  - componenttest/pom.xml
  - postgresql_scripts/dbchangelog-master.xml
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - architecture/cross-cutting.md
  - stack/stack.md
---

# Module layout

## Top-level modules

- `service/` — main reactive Spring Boot service and unit tests.
- `componenttest/` — separate Maven module with Cucumber, Serenity, and Testcontainers-backed component tests.
- `postgresql_scripts/` — Liquibase changelog plus checked-in SQL table scripts.
- `helm/` and `infrastructure/` — deployment and infrastructure assets.
- `spec/` — checked-in spec/config directory; no populated OpenAPI document was found during this trace.

## Service package tree

```text
net.apmoller.crb.telikos.microservices.smds.facilities
├── application
│   ├── controller
│   ├── mapper
│   ├── model
│   ├── service
│   ├── util
│   └── validator
├── common
│   ├── config
│   ├── constants
│   └── exceptions
├── domain
│   ├── model
│   │   ├── cmd
│   │   ├── common
│   │   └── smds
│   └── service
└── infrastructure
    ├── config
    ├── connector
    ├── contract/database
    ├── integrator
    ├── mapper
    └── repository
```

## Related

- [[architecture/cross-cutting]]
- [[stack/stack]]
