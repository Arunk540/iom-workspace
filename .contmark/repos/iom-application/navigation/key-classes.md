---
category: navigation
title: Key classes by concern
summary: Groups the most important classes by responsibility so agents can jump directly to the relevant module.
primary_for: [key-class-map]
mentions: [concern map, core classes, important files, module owners]
scenarios:
  - which class owns
  - find core model
  - find client class
  - find repository class
  - find validation class
capabilities: [concern-mapping]
domains: [iom-application]
entities: [ServicePlan, ReferenceDataFetcher, WebClientConfiguration]
sources:
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/models/ServicePlan.kt
  - reference-cache/src/main/kotlin/com/maersk/iom/config/ReferenceDataFetcher.kt
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/config/WebClientConfiguration.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - architecture/modules.md
  - navigation/entry-points.md
---
| Concern | Class |
|---|---|
| Aggregate root | `com.maersk.iom.order.domain.models.ServicePlan` |
| Booking subtree | `...models.Booking` and `...models.BookingEquipment` |
| Pricing and product tree | `...models.ProductOffer` |
| Validation SPI | `com.maersk.iom.validator.IOMValidator` |
| Date-order validation | `com.maersk.iom.validator.ServiceDatesValidator` |
| SAP/TMS delta validation | `com.maersk.iom.validator.ackandexecutionrules.IomSapTmsRulesValidator` |
| Resolver SPI | `com.maersk.iom.resolvers.Resolver` |
| Persisted-plan enrichment | `com.maersk.iom.resolvers.PersistedServicePlanResolver` |
| Party enrichment | `com.maersk.iom.resolvers.PartyResolver` |
| Facility enrichment | `com.maersk.iom.resolvers.FacilityResolver` |
| Commodity enrichment | `com.maersk.iom.resolvers.CommodityResolver` |
| Domain repository port | `com.maersk.iom.order.domain.OrderRepository` |
| Persistence adapter | `com.maersk.iom.order.persistence.adapter.OrderRepositoryImpl` |
| Main R2DBC entity | `com.maersk.iom.order.persistence.entity.ServicePlanEntity` |
| Reactive repository | `com.maersk.iom.order.persistence.adapter.ServicePlanEntityRepository` |
| Mongo document mirror | `com.maersk.iom.order.persistence.document.ServicePlanDocument` |
| Cache warmer | `com.maersk.iom.config.ReferenceDataFetcher` |
| External client wiring | `com.maersk.iom.webclient.config.WebClientConfiguration` |
| Feature-flag provider | `com.maersk.iom.common.features.LaunchDarklyFeatureProvider` |
| Request context | `com.maersk.iom.common.context.RequestContextProvider` |
