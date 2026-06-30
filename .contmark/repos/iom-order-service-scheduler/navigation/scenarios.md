---category: navigation
title: Scenarios
summary: Maps common operator and developer task phrases to the first class and method to inspect. Use this as the fast router before opening deeper runtime or integration files.
primary_for: [task-to-start-point-routing]
mentions: [scheduler-entry-points, repricing-workflow-execution, kafka-topic-publishing]
scenarios:
  - where does repricing start
  - archive job failing
  - offer cleanup failing
  - transform job stuck
  - which class sends kafka
  - route a task to code
capabilities: [navigation]
domains: [IOM]
entities: [RepricingWorkflow, OutboxMessage, ServicePlanEntity]
sources:
  - src/main/kotlin/com/maersk/iom/outbox/scheduler/RepricingScheduler.kt
  - src/main/kotlin/com/maersk/iom/outbox/scheduler/MessagePublishingScheduler.kt
  - src/main/kotlin/com/maersk/iom/archive/service/OrderArchivalService.kt
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - navigation/entry-points.md
  - navigation/key-classes.md
---

# Scenario routing

| Task phrase | Start here |
|---|---|
| repricing not starting | `RepricingScheduler.scheduleRepricing()` |
| repricing workflow failing | `RepricingWorkflowImpl.reprice()` |
| repricing api call failing | `WebIntegratorClientImpl.repriceBooking()` |
| outbox message not published | `MessagePublishingScheduler.checkAndPublishMessage()` |
| wrong producer selected | `MessageProducerFactory.processMessage()` |
| service plan topic issue | `ServicePlanEventsProducer.triggerEvent()` |
| domain event topic issue | `ServicePlanDomainEventsProducer.triggerEvent()` |
| booking external topic issue | `BookingStatusChangedProducer.triggerEvent()` |
| order archive failing | `OrderArchivalService.findAndArchiveServicePlanEntities()` |
| audit archive failing | `AuditArchivalService.findAndArchiveServicePlanEntities()` |
| blob upload issue | `BlobUploader.uploadServicePlanEntityToBlobStorage()` |
| offer cleanup failing | `OfferScheduler.runOfferCleanupJob()` |
| offer delete call failing | `OfferedServicePlanClientImpl.removeOfferedServicePlan()` |
| transform job failing | `TransformScheduler.runScheduledTask()` |
| temporal connection issue | `TemporalConfig.workflowServiceStubs()` |
