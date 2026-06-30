---
category: architecture
title: Modules and package tree
summary: Summarizes the multi-module Gradle structure, published artifacts, and the main package families owned by each module.
primary_for: [multi-module-package-tree]
mentions: [gradle modules, package tree, module structure, artifact map]
scenarios:
  - list all modules
  - show package tree
  - understand gradle layout
  - map module ownership
  - trace package family
capabilities: [module-topology]
domains: [iom-application]
entities: [application-model, iom-order-domain, iom-persistence]
sources:
  - settings.gradle.kts
  - build.gradle.kts
  - application-model/build.gradle.kts
  - application-validators/build.gradle.kts
  - iom-common/build.gradle.kts
  - iom-order-domain/build.gradle.kts
  - iom-persistence/build.gradle.kts
  - reference-cache/build.gradle.kts
  - reference-data-client/build.gradle.kts
  - resolvers/build.gradle.kts
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - architecture/cross-cutting.md
  - stack/stack.md
---
## Gradle modules

- `application-model`
- `application-validators`
- `iom-common`
- `iom-order-domain`
- `iom-persistence`
- `reference-cache`
- `reference-data-client`
- `resolvers`

## Dependency shape

- `application-model` depends on `iom-common` and `iom-order-domain`.
- `application-validators` pulls together DTOs, reference clients/cache, common enums, and domain models.
- `iom-order-domain` is the core model/port module with the lightest dependency footprint.
- `iom-persistence` depends on `iom-common` + `iom-order-domain` and adds R2DBC, Mongo, and Liquibase support.
- `reference-cache` and `resolvers` sit above `reference-data-client` and domain types.
- `reference-data-client` centralizes all outbound WebClient integrations.

## Package tree

### `application-model`
- `com.maersk.iom.application.annotation`
- `com.maersk.iom.application.contextProviders`
- `com.maersk.iom.application.exception`
- `com.maersk.iom.application.model.businessrules`
- `com.maersk.iom.application.model.dto.v1`
- `com.maersk.iom.application.model.dto.v2`
- `com.maersk.iom.application.model.dto.v2.location`
- `com.maersk.iom.application.model.dto.v2.offer`
- `com.maersk.iom.application.model.dto.v2.routing`
- `com.maersk.iom.application.model.dto.v3`
- `com.maersk.iom.application.model.dto.v3.dashboard`
- `com.maersk.iom.application.model.dto.v3.party`
- `com.maersk.iom.application.model.dto.v3.routing`
- `com.maersk.iom.application.model.dto.v3.vas`
- `com.maersk.iom.application.model.external`
- `com.maersk.iom.application.model.external.businessRules`
- `com.maersk.iom.application.model.external.telikos`
- `com.maersk.iom.application.model.validators`
- `com.maersk.iom.application.model.validators.v3`
- `com.maersk.iom.application.model.views`

### `application-validators`
- `com.maersk.iom.validator`
- `com.maersk.iom.validator.ackandexecutionrules`
- `com.maersk.iom.validator.ackandexecutionrules.config`
- `com.maersk.iom.validator.ackandexecutionrules.utils`
- `com.maersk.iom.validator.ackandexecutionrules.validators`
- `com.maersk.iom.validator.businessRules`
- `com.maersk.iom.validator.v2`
- `com.maersk.iom.validator.v3`

### `iom-common`
- `com.maersk.iom.common`
- `com.maersk.iom.common.context`
- `com.maersk.iom.common.converter`
- `com.maersk.iom.common.enums`
- `com.maersk.iom.common.exception`
- `com.maersk.iom.common.features`
- `com.maersk.iom.common.mapper`
- `com.maersk.iom.common.utils`
- `com.maersk.iom.common.validator`

### `iom-order-domain`
- `com.maersk.iom.order.domain`
- `com.maersk.iom.order.domain.event`
- `com.maersk.iom.order.domain.models`
- `com.maersk.iom.order.domain.models.requests`
- `com.maersk.iom.order.domain.models.responses`
- `com.maersk.iom.order.domain.models.vas`
- `com.maersk.iom.openapi.webintegrator.v1.model`

### `iom-persistence`
- `com.maersk.iom.order.persistence.adapter`
- `com.maersk.iom.order.persistence.config`
- `com.maersk.iom.order.persistence.document`
- `com.maersk.iom.order.persistence.entity`
- `com.maersk.iom.order.persistence.mapper`

### `reference-cache`
- `com.maersk.iom.config`

### `reference-data-client`
- `com.maersk.iom.webclient`
- `com.maersk.iom.webclient.businessRules`
- `com.maersk.iom.webclient.charges`
- `com.maersk.iom.webclient.commodity`
- `com.maersk.iom.webclient.config`
- `com.maersk.iom.webclient.containertype`
- `com.maersk.iom.webclient.customer`
- `com.maersk.iom.webclient.documentScanner`
- `com.maersk.iom.webclient.facility`
- `com.maersk.iom.webclient.location`
- `com.maersk.iom.webclient.mapper`
- `com.maersk.iom.webclient.notifications`
- `com.maersk.iom.webclient.oceanCarrier`
- `com.maersk.iom.webclient.offer`
- `com.maersk.iom.webclient.reasons`
- `com.maersk.iom.webclient.routing`
- `com.maersk.iom.webclient.telikos`
- `com.maersk.iom.webclient.vas`
- `com.maersk.iom.webclient.vatpartner`

### `resolvers`
- `com.maersk.iom.resolvers`
