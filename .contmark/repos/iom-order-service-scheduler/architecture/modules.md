---
category: architecture
title: Modules
summary: Package tree for the scheduler repo. The codebase is organized around schedulers, outbound integrations, and support configuration rather than inbound APIs.
primary_for: [package-structure-map]
mentions: [scheduler-entry-points, outbound-integration-layout, repricing-workflow-execution]
scenarios:
  - repo package tree
  - where code lives
  - module layout
  - package ownership
  - find scheduler package
capabilities: [architecture]
domains: [IOM]
entities: [OutboxMessage, ServicePlan, AuditEntity]
sources:
  - src/main/kotlin/com/maersk/iom/IomOrderSchedulerApplication.kt
  - src/main/kotlin/com/maersk/iom/archive/OrderArchivalScheduler.kt
  - src/main/kotlin/com/maersk/iom/outbox/scheduler/MessagePublishingScheduler.kt
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - architecture/cross-cutting.md
  - navigation/key-classes.md
---

# Package tree

```text
com.maersk.iom
├── IomOrderSchedulerApplication
├── archive
│   ├── OrderArchivalScheduler
│   ├── AuditArchivalScheduler
│   └── service
│       ├── OrderArchivalService
│       ├── AuditArchivalService
│       └── BlobUploader
├── config
│   ├── TemporalConfig
│   ├── KafkaProducerConfig
│   ├── AzureBlobStorageConfiguration
│   ├── SecurityConfig
│   ├── IomMapperConfig
│   └── LaunchDarklyLocalConfig
├── messaging
│   ├── ServicePlanEventsProducer
│   ├── ServicePlanDomainEventsProducer
│   ├── BookingStatusChangedProducer
│   ├── ServicePlanMapper
│   ├── ServicePlanDomainEventMapper
│   └── BookingExternalMapper
├── offer
│   ├── OfferScheduler
│   ├── OfferedServicePlanClientImpl
│   ├── WebIntegratorClientImpl
│   ├── WebClientConfiguration
│   └── OfferRecordsCleanUpResponse
├── order/domain/extension
│   └── ReceiveChannelPartyExtension
├── outbox/scheduler
│   ├── MessagePublishingScheduler
│   ├── MessageProducerFactory
│   └── RepricingScheduler
├── transform
│   └── TransformScheduler
└── workflows/repricing
    ├── RepricingWorkflow
    ├── RepricingWorkflowImpl
    ├── RepricingActivity
    └── RepricingActivityImpl
```

## Resource tree

- `src/main/resources/application*.yml|yaml` holds scheduler toggles, Temporal, Kafka, OAuth, tracing, and profile overrides.
- `src/main/resources/avro/` holds `ServicePlan`, `EventNotification`, and `BookingExternal` schemas.
- `src/main/resources/FeatureCatalog.json` seeds local LaunchDarkly flags.
- `src/main/resources/translate/` holds Jolt transform specs used by dependent code generation/mapping workflows.
