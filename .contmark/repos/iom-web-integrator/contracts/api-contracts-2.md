---
category: contracts
title: API contracts — orders and preferences
summary: Inbound HTTP contracts for order lifecycle, exports, repricing application, customer behaviour, and preference CRUD.
primary_for: [api-contracts-orders]
mentions: [api, contracts, orders, preferences]
scenarios:
  - order contracts
  - orders api contract
  - preferences contracts
  - status contracts
  - download api contract
capabilities: [api-contracts]
domains: [Web Integrator]
entities: [ServicePlanResponse, PreferenceSchema, Content]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/controller
  - src/main/resources/openapi/iom-web-integrator-openapi-spec-v1.yaml
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - contracts/api-contracts-1.md
  - navigation/entry-points-2.md
---
# API contracts — part 2

- `GET /service-plans/{servicePlanNumber}` — query params `includeDraftRevenues` and `includeCosts` → `ServicePlan`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:36)
- `POST /service-plans/search` — `ServicePlanStatusSearchQuery` + `page` + `size` → `Content`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:54)
- `POST /service-plans/multi-search` — `MultiSearchQuery` + `page` + `size` → `MultiSearchContent`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:69)
- `POST /service-plans/search/download` — `ServicePlanStatusSearchQuery` → `ResponseEntity<ByteArrayResource>`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:86)
- `POST /service-plans/containers/download` — `ServicePlanStatusSearchQuery` → `ResponseEntity<ByteArrayResource>`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:108)
- `POST /service-plans` — `ServicePlan` → `ServicePlanResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:130)
- `PUT /service-plans/{servicePlanNumber}/amend` — `ServicePlan` → `ServicePlanResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:140)
- `PATCH /service-plans/{servicePlanNumber}` — `StatusUpdateRequest` → `ServicePlanResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:152)
- `PATCH /service-plans/{servicePlanNumber}/charges` — `ServicePlan` → `ServicePlanResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:165)
- `PATCH /service-plans/{servicePlanNumber}/containers` — `ServicePlan` → `ServicePlanResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:185)
- `GET /service-plans/{servicePlanNumber}/status` — no body → `ServicePlanStatusResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:193)
- `PATCH /service-plans/{servicePlanNumber}/finops-action-completed` — no body → `ServicePlanResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:201)
- `PATCH /service-plans/{servicePlanNumber}/soft-close` — no body → `202 Accepted` with empty response. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:209)
- `POST /service-plans/finops-ref-numbers/update` — `FrnUpdateRequest` → `FrnUpdateResponse` with 200/206/400 outcomes. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:221)
- `GET /cust-behaviour?customer_id=...` — no body → raw JSON string. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:238)
- `GET /service-plans/{servicePlanNumber}/container-associations` — no body → `Flux<ContainerAssociationsResponse>`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:255)
- `PATCH /service-plans/{servicePlanNumber}/ocean-transportation-details` — `VesselDatesUpdateRequest` → `ServicePlanResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:270)
- `POST /service-plans/reprice` — `RepriceBookingsRequest` → `RepriceBookingsResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:294)
- `GET /preferences` and `POST /preferences` — product-scoped preference list/create contracts. (source: src/main/resources/openapi/iom-web-integrator-openapi-spec-v1.yaml:778)
- `PATCH /preferences/{preferenceId}` and `DELETE /preferences/{preferenceId}` — single-preference update/delete contracts. (source: src/main/resources/openapi/iom-web-integrator-openapi-spec-v1.yaml:825)
