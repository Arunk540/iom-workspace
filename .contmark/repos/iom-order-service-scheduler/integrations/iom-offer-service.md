---
category: integrations
title: IOM Offer Service
summary: Offer cleanup calls the IOM Offer Service over OAuth-protected WebClient and deletes old offered-service-plan records by age.
primary_for: [offer-cleanup-http]
mentions: [offered-service-plan-cleanup, oauth-webclient-wiring, external-http-timeouts]
scenarios:
  - offer cleanup failing
  - delete offered plans
  - offer service call
  - offered plan cleanup
  - where offer api lives
capabilities: [offer-cleanup]
domains: [IOM]
entities: [OfferRecordsCleanUpResponse]
peer_systems: [iom-offer-service]
direction: outbound
protocol: rest
topic_or_endpoint: DELETE ?numberOfDays=
sources:
  - src/main/kotlin/com/maersk/iom/offer/OfferScheduler.kt
  - src/main/kotlin/com/maersk/iom/offer/OfferClient.kt
  - src/main/kotlin/com/maersk/iom/offer/WebClientConfiguration.kt
  - src/main/resources/application.yaml
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - navigation/entry-points.md
  - operations/failure-model.md
---

# IOM Offer Service integration

- `OfferScheduler` is only created when `offers.cleanup.scheduler.active=true` and runs on the configured cron (source: src/main/kotlin/com/maersk/iom/offer/OfferScheduler.kt:13; source: src/main/resources/application.yaml:304)
- `WebClientConfiguration.offeredServicePlanClient()` builds the client from `services.iomofferservice.base-url` and applies the `iomOauth` client-credentials filter (source: src/main/kotlin/com/maersk/iom/offer/WebClientConfiguration.kt:59; source: src/main/resources/application.yaml:59)
- `OfferedServicePlanClientImpl.removeOfferedServicePlan(numberOfDays)` issues `DELETE` and adds `numberOfDays` as a query parameter (source: src/main/kotlin/com/maersk/iom/offer/OfferClient.kt:22)
- Non-2xx responses are turned into `RuntimeException("Error response from API: ...")` before the scheduler `block()` completes (source: src/main/kotlin/com/maersk/iom/offer/OfferClient.kt:27; source: src/main/kotlin/com/maersk/iom/offer/OfferScheduler.kt:25)
- Shared client timeouts come from `services.external.connect-timeout-ms`, `read-timeout-ms`, and `write-timeout-ms` through the common Reactor Netty client (source: src/main/kotlin/com/maersk/iom/offer/WebClientConfiguration.kt:44; source: src/main/resources/application.yaml:61)
