---
category: integrations
title: Order service integration
summary: Order-service is the primary downstream system for service-plan CRUD, status, search, incidents, finops, container associations, and vessel-date updates.
primary_for: [order-service-integration]
mentions: [order service, service plans, status, containers]
scenarios:
  - order service calls
  - service plan downstream
  - order api integration
  - container associations backend
  - order search backend
capabilities: [integration-order-service]
domains: [Orders]
entities: [V3ServicePlanModel, Content, ServicePlanStatusResponse]
sources:
  - src/main/resources/application.yml
  - src/main/kotlin/com/maersk/iom/webintegrator/config/WebClientConfiguration.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/OrderClient.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - runtime/order-lifecycle-flow.md
  - operations/retries.md
---
# Order service integration

- Base URL is `services.order.url = ${services.order.base-url}/v3/service-plans`; default host points to `iom-order-service.dev.maersk-digital.net`. (source: src/main/resources/application.yml:42)
- `WebClientConfiguration.orderClient(...)` binds that base URL to the `orderClient` bean. (source: src/main/kotlin/com/maersk/iom/webintegrator/config/WebClientConfiguration.kt:101)
- CRUD/search endpoints include `/{servicePlanNumber}`, `/search?page&size`, `/multi-search?page&size`, `/search/download`, and `/containers/download`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/OrderClient.kt:73)
- Mutating endpoints include `/{servicePlanNumber}/amend`, `/{servicePlanNumber}/charges`, `/{servicePlanNumber}/containers`, and `/{servicePlanNumber}/ocean-transportation-details`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/OrderClient.kt:113)
- Finops and side-channel endpoints include `/{servicePlanNumber}/finOpsAction?completed=...`, `/finops-ref-numbers/update`, `/cust-behaviour`, `/{servicePlanNumber}/incident`, and `/{servicePlanNumber}/container-associations`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/OrderClient.kt:289)
- Most write-heavy calls use centralized retries; status and container-association reads do not add a custom `onStatus` handler and rely on WebClient default exceptions. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/OrderClient.kt:92)
