---category: navigation
title: Scenario routing
summary: The codebase clusters around order mutations, dashboard/search queries, Kafka event handling, and reporting/export utilities. Most tasks can start from one controller or consumer method and then follow service/domain delegation.
primary_for: [task-routing]
mentions: [where-to-start, controller-methods, service-methods, event-flows, exports]
scenarios:
  - update service plan
  - search dashboard results
  - debug booking event
  - export amendment file
  - check customer history
  - find request routing
  - trace task routing
capabilities: [navigation]
domains: [order]
entities: [ServicePlan, Booking, CustomerHistory, Incident]
sources:
  - src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt
  - src/main/kotlin/com/maersk/iom/controller/v3/V3DashboardController.kt
  - src/main/kotlin/com/maersk/iom/messaging/BookingConsumer.kt
  - src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt
  - src/main/kotlin/com/maersk/iom/service/SearchService.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - navigation/entry-points.md
  - navigation/key-classes.md
  - runtime/order-creation-flow.md
---
- create or update order → `V3OrderController.update` → `OrderServiceImpl.createOrUpdateOrder`
- patch booking party or status → `V3OrderController.patchServicePlan` → `OrderServiceImpl.patch`
- amend confirmed booking → `V3OrderController.amend` → `OrderServiceImpl.amendOrder`
- update execution charges → `ExecutionChargesController.addExecutionCharges` → `ExecutionChargeService.addExecutionChargesToServicePlan`
- search dashboard → `V3DashboardController.getServicePlansMatchingQuery` → `SearchService.findOrders`
- multi search by identifiers → `V3DashboardController.searchServicePlans` → `SearchService.findBookings`
- export dashboard excel → `V3DashboardController.getServicePlansMatchingQueryAndExport` → `ExportExcelDataService.findOrdersAndExportToExcel`
- export container execution file → `V3DownloadController.getServicePlansContainersExport` → `BookingEquipmentService.exportBookingEquipmentsToExcel`
- inspect reference enums → `ReferenceDataController.*`
- inspect error catalog → `OrderServiceErrorCodesController.getOrderServiceError`
- debug booking status event → `BookingConsumer.listenForBookingMessage` → `EventDispatcher.dispatch` → `BookingUpdatesEventHandler.handleEvent`
- debug pricing update event → `BookingConsumer.listenForBookingMessage` → `PricingUpdatesEventHandler.handleEvent`
- audit domain event → `ServicePlanDomainEventConsumer.listenForServicePlanNotification` → `AuditService.audit`
- rebuild customer history → `CustomerBookingHistoryEventConsumer.transformAndSaveCustomerBookingHistory`
- rerun customer history batch → `CustomerBookingHistoryScheduler.triggerScheduler`
- inspect search SQL criteria → `ServicePlanSearchRequest.query` / `ServicePlanMultiSearchRequest.multiSearchQuery`
- inspect incident validation → `IncidentController.createIncident` → `IncidentService.isValidIncident`
- inspect outbox write → `OrderServiceImpl.handleDomainEvents` → `DomainEventHandlerFacade.handle` → `OutboxStrategy.handle`
