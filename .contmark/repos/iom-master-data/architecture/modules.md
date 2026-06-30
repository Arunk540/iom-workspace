---
category: architecture
title: Gradle modules and package layout
summary: iom-master-data is a root aggregator with controller/persistence submodules per master-data domain plus a service host module.
primary_for: [multi-module-package-tree]
mentions: [gradle, modules, packages, controller, persistence]
scenarios:
  - see module layout
  - map package tree
  - find submodule boundaries
  - understand gradle includes
  - see domain ownership
capabilities: [module-mapping]
domains: [platform]
entities: [settings.gradle.kts, service, location, commodity, vendors]
sources:
  - settings.gradle.kts
  - build.gradle.kts
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - architecture/cross-cutting.md
  - stack/stack.md
  - navigation/key-classes.md
---

# Module tree

- `common/` — shared code under `com.maersk.iom.master.data.common` and imported Kafka/location support. 
- `service/` — Spring Boot host under `com.maersk.iom.master.data` with `config`, `country`, `currency`, `customs`, `emergencyTemp`, `finops`, `carrier`, `imoclassification`, `layouttypes`, `reasons`, and `vas` packages.
- `location/`
  - `location-controller/` — `...location.controller`, `...location.service`, `...location.webclient`, `...location.adapter`, `...location.model`
  - `location-persistence/` — `...location.persistence`, `...location.persistence.relational`, `...location.persistence.adapter`
- `container/`
  - `container-controller/` — `...container.controller`, `...container.controller.service`, `...container.controller.startup`
  - `container-persistence/` — `...container.persistence`, `...container.persistence.relational`
- `commodity/`
  - `commodity-controller/` — `...commodity.controller`, `...commodity.controller.service`, `...commodity.controller.startup`, `...commodity.controller.webclient`
  - `commodity-persistence/` — `...commodity.persistence`, `...commodity.persistence.relational`
- `charges/`
  - `charges-controller/` — `...charges.controller`, `...charges.controller.service`, `...charges.controller.service.vatpartner`, `...charges.controller.service.uom`
  - `charges-persistence/` — `...charges.persistance`, `...charges.persistance.repository`, `...charges.persistance.mapper`
- `vendors/`
  - `vendor-controller/` — `...vendor.controller`, `...vendor.service`, `...vendor.config`
  - `vendor-persistence/` — `...vendor.persistence.entity`, `...vendor.persistence.repository`, `...vendor.persistence.mapper`
- `sales/`
  - `sales-controller/` — `...sales.controller.controller`, `...sales.controller.service`, `...sales.controller.model`
  - `sales-persistence/` — `com.maersk.iom.master.data.persistence`
- `diesel-price-ranges/`
  - `diesel-price-ranges-controller/` — `...dieselpriceranges.controller`, `...dieselpriceranges.controller.service`
  - `diesel-price-ranges-persistence/` — `...dieselpriceranges.persistence.entity`, `...dieselpriceranges.persistence.repository`
- `vessel/`
  - `vessel-controller/` — `...vessel.controller`, `...vessel.controller.service`, `...vessel.controller.validation`
  - `vessel-persistence/` — `...vessel.persistence.entity`, `...vessel.persistence.repository`

## Aggregation notes

- The root `settings.gradle.kts` includes `common`, `service`, and every controller/persistence pair for location, container, commodity, charges, vendors, sales, diesel price ranges, and vessel.
- The root `build.gradle.kts` aggregates JaCoCo across the same modules and disables a runnable boot jar at the aggregator level.
