---category: navigation
title: Task scenarios
summary: Maps common engineering tasks to the best starting class and method inside the iom-application library stack.
primary_for: [task-scenario-routing]
mentions: [start-here, class.method, resolver flow, validator flow, persistence flow]
scenarios:
  - trace amend flow
  - find db save
  - follow resolver call
  - debug date validation
  - locate external client
  - find the right scenario
  - match a user scenario
capabilities: [task-routing]
domains: [iom-application]
entities: [OrderService, OrderRepositoryImpl, ServiceDatesValidator]
sources:
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/OrderService.kt
  - iom-persistence/src/main/kotlin/com/maersk/iom/order/persistence/adapter/OrderRepositoryImpl.kt
  - application-validators/src/main/kotlin/com/maersk/iom/validator/ServiceDatesValidator.kt
  - resolvers/src/main/kotlin/com/maersk/iom/resolvers/PersistedServicePlanResolver.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - navigation/entry-points.md
  - runtime/order-processing.md
---
| Task phrase | Start here |
|---|---|
| create or update a service plan | `OrderService.createOrUpdateOrder()` |
| amend an existing order | `OrderService.amendOrder()` |
| update order status | `OrderService.updateServicePlanStatus()` |
| persist a domain object | `OrderRepositoryImpl.save()` |
| load and auto-upgrade stored plans | `OrderRepositoryImpl.findById()` |
| resolve existing plan before amend | `PersistedServicePlanResolver.resolve()` |
| resolve parties from customer codes | `PartyResolver.resolve()` |
| resolve facilities from geo ids | `FacilityResolver.resolve()` |
| validate service date sequencing | `ServiceDatesValidator.validate()` |
| inspect SAP/TMS delta checks | `IomSapTmsRulesValidator.validate()` |
| fetch offered service plan | `OfferedServicePlanClientImpl.getOfferedServicePlan()` |
| request routing options | `RoutingClientImpl.getRoutings()` |
| refresh cached commodity data | `ReferenceDataFetcher.refreshCommodityCache()` |
| send booking to Telikos TMS | `TelikosClientImpl.sendToTms()` |
| post a notification webhook | `NotificationClientImpl.sendNotification()` |
