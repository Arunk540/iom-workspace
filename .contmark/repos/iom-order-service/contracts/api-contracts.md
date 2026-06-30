---category: contracts
title: API contracts
summary: The service exposes v2 dashboard/reference endpoints, a larger v3 order surface, incident creation, execution-charge writes, and file download/report endpoints. Controller methods are thin adapters over domain or service calls, with DTO mapping and role checks at the edge.
primary_for: [api-contracts]
mentions: [v2, v3, search, patch, amend, exports]
scenarios:
  - list rest endpoints
  - find write endpoint
  - find search endpoint
  - find export endpoint
  - find incident endpoint
  - find the api contracts
  - check contracts definitions
capabilities: [contracts]
domains: [api]
entities: [V3ServicePlanModel, V3PatchRequestModel, V3ServicePlanStatusSearchQuery, IncidentModel]
sources:
  - src/main/kotlin/com/maersk/iom/controller/v2/DashboardController.kt
  - src/main/kotlin/com/maersk/iom/controller/v2/ReferenceDataController.kt
  - src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt
  - src/main/kotlin/com/maersk/iom/controller/v3/V3DashboardController.kt
  - src/main/kotlin/com/maersk/iom/controller/v3/V3DownloadController.kt
  - src/main/kotlin/com/maersk/iom/controller/v3/ExecutionChargesController.kt
  - src/main/kotlin/com/maersk/iom/controller/OrderServiceErrorCodesController.kt
  - src/main/kotlin/com/maersk/iom/incident/controller/IncidentController.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - navigation/entry-points.md
  - runtime/order-creation-flow.md
  - runtime/dashboard-query-flow.md
---
## V3 order surface
- `POST /v3/service-plans` → create or update service plan from `V3ServicePlanModel`, returns `V3ServicePlanResponseModel(servicePlanNumber)`. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:64-84)
- `PATCH /v3/service-plans/{servicePlanNumber}` → patch one branch of `V3PatchRequestModel`, returns `V3ServicePlanResponseModel`. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:86-148)
- `PUT /v3/service-plans/{servicePlanNumber}/amend` → amend from `V3ServicePlanModel`, returns `V3ServicePlanResponseModel`. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:150-171)
- `GET /v3/service-plans/{servicePlanNumber}` → fetch full `V3ServicePlanModel`. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:173-193)
- `GET /v3/service-plans/{servicePlanNumber}/status` → fetch status-focused `V3ServicePlanModel` with JSON view. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:212-233)
- `PATCH /v3/service-plans/{servicePlanNumber}/ocean-transportation-details` → update vessel date request, returns `V3ServicePlanResponseModel`. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:235-261)
- `PATCH /v3/service-plans/{servicePlanNumber}/charges` → update charges from `V3ServicePlanModel`, returns `V3ServicePlanResponseModel`. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:263-284)
- `PATCH /v3/service-plans/{servicePlanNumber}/containers` → update containers from `V3ServicePlanModel`, returns `V3ServicePlanResponseModel`. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:286-303)
- `PATCH /v3/service-plans/{servicePlanNumber}/finOpsAction?completed=` → toggles FinOps completion, returns service-plan number wrapper. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:305-320)
- `POST /v3/service-plans/finops-ref-numbers/update` → bulk FRN update from `FrnUpdateRequest`, returns `FrnUpdateResponse`. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:322-338)
- `GET /v3/service-plans/cust-behaviour?customer_id=` → returns customer-history JSON string. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:340-353)
- `GET /v3/service-plans/containers?...` → returns `Flux<ContainerDetails>`. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:355-372)
- `GET /v3/service-plans/{servicePlanNumber}/container-associations` → returns `Flux<ContainerAssociations>`. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:374-389)

## V3 dashboard and downloads
- `POST /v3/service-plans/search` → paged `Content` dashboard result from `V3ServicePlanStatusSearchQuery`. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3DashboardController.kt:87-117)
- `POST /v3/service-plans/multi-search` → paged `MultiSearchContent` plus unmatched identifier errors. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3DashboardController.kt:119-166)
- `POST /v3/service-plans/search/download` → XLSX download for dashboard results. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3DashboardController.kt:168-212)
- `POST /v3/service-plans/containers/download` → XLSX download for booking container execution status. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3DownloadController.kt:28-60)
- `PUT /service-plans/{servicePlanNumber}/execution-charges` → adds execution charges from `ExecutionChargeRequest`. (source: src/main/kotlin/com/maersk/iom/controller/v3/ExecutionChargesController.kt:29-101)
- `POST /v3/service-plans/{servicePlanNumber}/incident` → creates incident from `IncidentModel`, returns `IncidentResponse`. (source: src/main/kotlin/com/maersk/iom/incident/controller/IncidentController.kt:28-68)

## V2 and misc
- `POST /service-plans/search` → deprecated v2 paged search. (source: src/main/kotlin/com/maersk/iom/controller/v2/DashboardController.kt:29-41)
- `GET /service-plans/stage-count` → v2 stage counters. (source: src/main/kotlin/com/maersk/iom/controller/v2/DashboardController.kt:43-46)
- `GET /reference-data/partyMasterRoles|locationFunctions|productTypes|cargoServiceTypes|WeightUnits|documentTypes` → enum/reference lists. (source: src/main/kotlin/com/maersk/iom/controller/v2/ReferenceDataController.kt:11-42)
- `GET /error-codes?errorCode=` → specific or full error catalog. (source: src/main/kotlin/com/maersk/iom/controller/OrderServiceErrorCodesController.kt:24-44)
