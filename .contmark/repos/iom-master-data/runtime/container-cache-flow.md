---
category: runtime
title: Container startup cache flow
summary: How container types are loaded into memory and served from the warm cache.
primary_for: [container-cache-flow]
mentions: [container, startup-load, iso-code, cache, read-only]
scenarios:
  - trace container cache
  - see startup load
  - inspect container fallback
  - find iso lookup
  - see read-only path
capabilities: [runtime-flow]
domains: [container]
entities: [ContainerTypeController, ContainerTypeServiceImpl, ContainerLoadService]
sources:
  - container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/ContainerTypeController.kt
  - container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/service/ContainerTypeServiceImpl.kt
  - container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/startup/ContainerLoadService.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - domain/container.md
  - contracts/db-schemas.md
  - navigation/scenarios.md
---

# Flow

1. `ContainerLoadService` subscribes to `loadContainerData()` during initialization because the module expects roughly a dozen container rows and prefers in-memory serving. (source: container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/startup/ContainerLoadService.kt:22)
2. `loadContainerData()` reads all records through `ContainerRepositoryExtended`, maps them with `ContainerTypeMapper`, and stores them in a map keyed by `isoContainerSizeTypeCd`. (source: container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/startup/ContainerLoadService.kt:27)
3. `GET /container-types/{isoContainerSizeTypeCd}` uses `ContainerTypeServiceImpl.getContainerTypeByISOCd`, which filters the cached stream and raises `NotFoundException` when empty. (source: container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/ContainerTypeController.kt:50; container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/service/ContainerTypeServiceImpl.kt:23)
4. `GET /container-types` applies request-flag predicates, defaulting to exclude customs-only container rows unless `includeCustomsContainer=true`. (source: container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/ContainerTypeController.kt:61)
5. If startup caching failed, `getContainerTypes` logs a warning and repopulates from the database on demand. (source: container/container-controller/src/main/kotlin/com/maersk/iom/master/data/container/controller/startup/ContainerLoadService.kt:43)
