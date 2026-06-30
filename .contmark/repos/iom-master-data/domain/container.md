---
category: domain
title: Container type reference data
summary: Container provides read-only container type lookups backed by startup-loaded data from the persistence layer.
primary_for: [container-type-reference]
mentions: [container-types, startup-load, iso-code, flags]
scenarios:
  - find container endpoint
  - trace container cache
  - see container table
  - inspect iso lookup
  - find startup loader
capabilities: [domain-summary]
domains: [container]
entities: [ContainerTypeController, ContainerLoadService, ContainerTypeEntity]
sources:
  - container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/ContainerTypeController.kt
  - container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/startup/ContainerLoadService.kt
  - container/container-persistence/src/main/kotlin/com/maersk/iom/master/data/container/persistence/ContainerTypeEntity.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - runtime/container-cache-flow.md
  - contracts/db-schemas.md
  - navigation/scenarios.md
---

# Scope

- Exposes legacy `/containerTypes` aliases and preferred `/container-types` endpoints.
- Loads a small container reference set once at startup and falls back to the DB only if the cache was not initialized.

## Data model

- `ContainerTypeEntity` captures ISO size/type code, operational flags, payload/tare weights, display order, and customs-container marker.

## Runtime shape

- `ContainerTypeServiceImpl` delegates all reads to `ContainerLoadService`.
- The module is read-only; no mutation or tracking logic is present.
