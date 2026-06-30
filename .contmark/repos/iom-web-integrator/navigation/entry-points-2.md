---
category: navigation
title: Entry points — order and preference APIs
summary: Second half of the inbound REST surface, covering order lifecycle, repricing, exports, and user-preference CRUD.
primary_for: [entry-point-map]
mentions: [controllers, orders, preferences, reprice, status]
scenarios:
  - order endpoint map
  - preference endpoint map
  - status api map
  - where order updates land
  - find preference handler
capabilities: [http-entrypoints]
domains: [Web Integrator]
entities: [ServicePlanResponse, PreferenceSchema, RepriceBookingsRequest]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/controller
  - src/main/resources/openapi/iom-web-integrator-openapi-spec-v1.yaml
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - navigation/entry-points-1.md
  - contracts/api-contracts-2.md
---
# Entry points — part 2

- `POST /service-plans/search/download` → `OrderController.getServicePlansMatchingQueryAndExport`; exports dashboard results as Excel. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:86)
- `POST /service-plans/containers/download` → `OrderController.getContainersMatchingQueryAndExport`; exports container rows as Excel. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:108)
- `POST /service-plans` → `OrderController.createOrder`; persists a `ServicePlan` payload. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:130)
- `PUT /service-plans/{servicePlanNumber}/amend` → `OrderController.amendOrder`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:140)
- `PATCH /service-plans/{servicePlanNumber}` → `OrderController.updateOrderStatus`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:152)
- `PATCH /service-plans/{servicePlanNumber}/charges` → `OrderController.updateCharges`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:165)
- `PATCH /service-plans/{servicePlanNumber}/containers` → `OrderController.updateContainers`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:185)
- `GET /service-plans/{servicePlanNumber}/status` → `OrderController.getOrderStatus`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:193)
- `PATCH /service-plans/{servicePlanNumber}/finops-action-completed` → `OrderController.markFinOpsActionAsCompleted`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:201)
- `PATCH /service-plans/{servicePlanNumber}/soft-close` → `OrderController.updateSoftClosureStatus`; gated by `@PreAuthorize`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:209)
- `POST /service-plans/finops-ref-numbers/update` → `OrderController.updateFrn`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:221)
- `GET /cust-behaviour` → `OrderController.getCustomerBookingHistory`; requires `customer_id` query param. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:238)
- `GET /service-plans/{servicePlanNumber}/container-associations` → `OrderController.getContainerAssociations`; this is the one HTTP endpoint returning `Flux<ContainerAssociationsResponse>`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:255)
- `PATCH /service-plans/{servicePlanNumber}/ocean-transportation-details` → `OrderController.updateOceanVesselDates`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:270)
- `POST /service-plans/reprice` → `OrderController.updateChargesForRepriceableBookings`; batch applies repriced charges. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:294)
- `GET /preferences` → `UserPreferencesController.getUserPreferences`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/UserPreferencesController.kt:37)
- `POST /preferences` → `UserPreferencesController.createUserPreference`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/UserPreferencesController.kt:62)
- `PATCH /preferences/{preferenceId}` → `UserPreferencesController.updateUserPreference`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/UserPreferencesController.kt:88)
- `DELETE /preferences/{preferenceId}` → `UserPreferencesController.deleteUserPreference`; controller annotation omits the leading slash, while the OpenAPI spec advertises `/preferences/{preferenceId}`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/UserPreferencesController.kt:115)
