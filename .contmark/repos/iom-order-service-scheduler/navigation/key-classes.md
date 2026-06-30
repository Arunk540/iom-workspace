---
category: navigation
title: Key classes
summary: Concern-to-class map for the scheduler service. Use this to jump from a feature area to the owning class without loading whole packages.
primary_for: [concern-to-class-routing]
mentions: [scheduler-entry-points, repricing-web-integrator, kafka-event-brokers]
scenarios:
  - which class owns repricing
  - who uploads blobs
  - where kafka sends
  - where oauth clients live
  - which class archives orders
capabilities: [navigation]
domains: [IOM]
entities: [OutboxMessage, ArchivalMetaEntity, ServicePlan]
sources:
  - src/main/kotlin/com/maersk/iom/config/TemporalConfig.kt
  - src/main/kotlin/com/maersk/iom/offer/WebClientConfiguration.kt
  - src/main/kotlin/com/maersk/iom/archive/service/BlobUploader.kt
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - architecture/modules.md
  - navigation/scenarios.md
---

# Concern map

| Concern | Class |
|---|---|
| Spring boot entry | `IomOrderSchedulerApplication` |
| Temporal stubs/client | `TemporalConfig` |
| Repricing workflow contract + impl | `RepricingWorkflow`, `RepricingWorkflowImpl` |
| Repricing HTTP activity | `RepricingActivityImpl`, `WebIntegratorClientImpl` |
| Outbox poller | `MessagePublishingScheduler` |
| Outbox routing | `MessageProducerFactory` |
| Service-plan Kafka publish | `ServicePlanEventsProducer` |
| Domain-event Kafka publish | `ServicePlanDomainEventsProducer` |
| Booking-external Kafka publish | `BookingStatusChangedProducer` |
| Offer cleanup scheduler | `OfferScheduler` |
| Offer service HTTP client | `OfferedServicePlanClientImpl` |
| Shared WebClient/OAuth wiring | `WebClientConfiguration` |
| Order archival scheduler/service | `OrderArchivalScheduler`, `OrderArchivalService` |
| Audit archival scheduler/service | `AuditArchivalScheduler`, `AuditArchivalService` |
| Blob upload | `BlobUploader` |
| Azure blob bean wiring | `AzureBlobStorageConfiguration` |
| Scheduler-side transform | `TransformScheduler` |
| MapStruct defaults | `IomMapperConfig`, `ServicePlanMapper`, `ServicePlanDomainEventMapper` |
| Receive-channel enrichment | `Booking.enrichPartiesBasedOnReceiveChannel()` |
| Security gate | `SecurityConfig` |
| Local LaunchDarkly stub | `LaunchDarklyLocalConfig` |
