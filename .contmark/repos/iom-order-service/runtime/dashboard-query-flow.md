---
category: runtime
title: Dashboard query flow
summary: Dashboard requests map HTTP search DTOs into Criteria-backed domain search objects, then use SearchService and QueryService to read `service_plans` through R2DBC. Export paths layer Excel transformation and amendment-journey validation on top of the same query model.
primary_for: [dashboard-query]
mentions: [dashboard-controller, search-service, query-service, export, criteria]
scenarios:
  - trace dashboard search
  - trace multi search
  - trace dashboard export
  - trace container search
  - inspect query criteria
capabilities: [runtime-flow]
domains: [query]
entities: [ServicePlanSearchRequest, ServicePlanMultiSearchRequest, Page, ServicePlanSummary]
sources:
  - src/main/kotlin/com/maersk/iom/controller/v3/V3DashboardController.kt
  - src/main/kotlin/com/maersk/iom/service/SearchService.kt
  - src/main/kotlin/com/maersk/iom/service/QueryService.kt
  - src/main/kotlin/com/maersk/iom/model/Extensions.kt
  - src/main/kotlin/com/maersk/iom/validator/AmendmentJourneyValidator.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - contracts/api-contracts.md
  - integrations/master-data-and-rules.md
  - operations/failure-model.md
---
1. `V3DashboardController.getServicePlansMatchingQuery` validates page size, derives sort/pageable, rejects customer-journey access, applies role-based `isScmBooking` filtering, maps the request to domain, and delegates to `SearchService.findOrders(...)`. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3DashboardController.kt:87-117, src/main/kotlin/com/maersk/iom/controller/v3/V3DashboardController.kt:214-277)
2. `SearchService.findOrders(...)` logs the request, builds pageable, and delegates directly to `QueryService.findServicePlansMatchingCriteria(...)`. (source: src/main/kotlin/com/maersk/iom/service/SearchService.kt:67-72)
3. `ServicePlanSearchRequest.query()` composes Spring Data `Criteria` from identifiers, dates, country/product filters, parties, cities, finance/customs fields, SCM booking flag, and work-process filters; it throws validation when no valid filters are supplied. (source: src/main/kotlin/com/maersk/iom/model/Extensions.kt:202-257, src/main/kotlin/com/maersk/iom/model/Extensions.kt:266-394)
4. `QueryService.findServicePlansMatchingCriteria(...)` counts matching `ServicePlanEntity` rows, pages them through `R2dbcEntityTemplate`, transforms legacy versions when needed, and maps JSON to domain `ServicePlan`. (source: src/main/kotlin/com/maersk/iom/service/QueryService.kt:32-48, src/main/kotlin/com/maersk/iom/service/QueryService.kt:121-127, src/main/kotlin/com/maersk/iom/entity/ServicePlanEntityExtension.kt:7-16)
5. Multi-search uses `SearchService.findBookings(...)`; container-number requests bypass Criteria and run a PostgreSQL array-overlap SQL query on `container_numbers`. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3DashboardController.kt:119-166, src/main/kotlin/com/maersk/iom/service/SearchService.kt:75-85, src/main/kotlin/com/maersk/iom/service/QueryService.kt:63-119)
6. Export requests can default amendment-journey closure status to `OPEN`, run `AmendmentJourneyValidator`, then call `ExportExcelDataService.findOrdersAndExportToExcel(...)` for XLSX bytes. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3DashboardController.kt:168-212, src/main/kotlin/com/maersk/iom/validator/AmendmentJourneyValidator.kt:15-68)
