---category: integrations
title: Master-data and rules integrations
summary: Master data, planning-deadline, offers, amend/cancel reasons, and other reference calls are mostly hidden behind shared resolver/client libraries. The local code orchestrates them through `ResolverService`, `PlanningDeadlineClient`, `ReferenceDataFetcher`, and search/export helpers.
primary_for: [master-data-integration]
mentions: [resolver-service, planning-deadline, reference-data, offers, business-rules]
scenarios:
  - inspect master data call
  - inspect planning deadline
  - inspect amend reasons
  - inspect dashboard resolver
  - inspect external rule config
  - find master data calls
capabilities: [integrations]
domains: [reference-data]
entities: [ResolvedData, Resolver, PlanningDeadlineResponse, ReferenceDataFetcher]
sources:
  - src/main/resources/application.yaml
  - src/main/kotlin/com/maersk/iom/service/ResolverService.kt
  - src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt
  - src/main/kotlin/com/maersk/iom/incident/service/IncidentService.kt
  - src/main/kotlin/com/maersk/iom/service/ExportExcelDataService.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - runtime/order-creation-flow.md
  - runtime/dashboard-query-flow.md
  - operations/flags-and-lists.md
---
- YAML declares customer, location, VAS, charge, commodity, commodity-restrictions, mandatory-party, mandatory-document-reference, planning-deadline, containerType, facility, reefer, offered-service-plan, VAT partner, amend/cancel reasons, business-rules, oceanCarrier, diesel-slab, notification, and SCM-customer endpoints. (source: src/main/resources/application.yaml:97-160)
- `ResolverService` fan-outs every injected `Resolver<ServicePlan>` implementation and returns a shared `ResolvedData` object, logging partial failures without failing the chain. (source: src/main/kotlin/com/maersk/iom/service/ResolverService.kt:17-35)
- `OrderServiceImpl.addOrUpdatePlanningDeadlineDate` calls `PlanningDeadlineClient.getPlanningDeadline(...)` and adds the calculated deadline when present. (source: src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:232-244)
- `IncidentService` validates amend and cancel reason codes through `ReferenceDataFetcher.getAmendReason(...)` and `getCancelReason(...)`. (source: src/main/kotlin/com/maersk/iom/incident/service/IncidentService.kt:141-149)
- `ExportExcelDataService` depends on `ReferenceDataFetcher` for export-time enrichment while `SearchService.find(...)` can optionally resolve master data for v2 dashboard responses when `features.master.data.resolver.dashboard.enabled=true`. (source: src/main/kotlin/com/maersk/iom/service/ExportExcelDataService.kt:28-36, src/main/kotlin/com/maersk/iom/service/SearchService.kt:37-64, src/main/resources/application.yaml:268-270)
