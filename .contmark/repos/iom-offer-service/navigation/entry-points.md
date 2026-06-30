---category: navigation
title: Entry Points
summary: All HTTP and Kafka entry points for iom-offer-service
primary_for: [api-entry-point-inventory, controller-routing-map]
mentions: [IOMSearchQueryController, RatesController, OfferServiceCleanUpController, OfferServiceDetailsController, ErrorCodeController, ServicePlanDomainEventConsumer]
scenarios:
  - search for inland service plans
  - calculate rates for offer
  - get repriced offer response
  - delete stale offer records
  - consume service plan domain event
  - list the entry points
  - find an entry point
  - find request routing
  - trace task routing
capabilities: [offer-search, rate-calculation, reprice, offer-cleanup, kafka-consumption]
domains: [offer-management, rates, routing]
entities: [V3SearchRequest, CalculateRatesRequest, RepriceRequest, OfferedServicePlanDocument]
sources:
  - src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt
  - src/main/kotlin/com/maersk/iom/offer/controller/v3/RatesController.kt
  - src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceCleanUpController.kt
  - src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt
  - src/main/kotlin/com/maersk/iom/offer/messaging/ServicePlanDomainEventConsumer.kt
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [navigation/scenarios.md, navigation/key-classes.md, contracts/api-contracts.md]
---

## HTTP Controllers

### IOMSearchQueryController — `/v3`
- `POST /service-plans-queries` — new offer search; role `Booking` or `SCMBooking` (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt:46)
- `PUT  /service-plans-queries` — amend offer search; role `Booking` or `SCMBooking` (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt:70)
- `POST /service-plans-queries/stream` — SSE streaming offer search (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt:92)

All three converge on `getProposedServicePlans()` which calls `enrichSearchRequest → persistSearchRequest → validateSearchRequestBasicValidator → searchForServicePlans` (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/IOMSearchQueryController.kt:107)

### RatesController — `/v3`
- `POST /rates/calculateRates` — calculate rates for an offered service plan; delegates to `RatesService.calculateRates` (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/RatesController.kt:46)

### OfferServiceDetailsController — `/v3`
- `GET  /offered-service-plan/{id}` — fetch offer details; role `permitted-role` (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt:61)
- `POST /offered-service-plan/{id}/equipments/delete` — soft-delete equipment from plan (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt:86)
- `GET  /search-request/{searchRequestId}` — fetch persisted search request (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt:108)
- `GET  /service-plans-queries/{id}/repriced-offer` — reprice (deprecated GET form) (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt:123)
- `POST /service-plans-queries/{id}/repriced-offer` — reprice with optional additional charges (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceDetailsController.kt:151)

### OfferServiceCleanUpController — `/v3`
- `DELETE /offered-service-plan?numberOfDays=N` — removes unmapped offers older than N days; `permitAll()` (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceCleanUpController.kt:34)

### ErrorCodeController — `/v3`
- `GET /error-codes` — returns static error code catalog (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/ErrorCodeController.kt)

## Kafka Consumer

### ServicePlanDomainEventConsumer
- Topic: `${kafka.service-plan-domain-event.topic}` (default `iom-serviceplan-domain-event-topic.local.v1`) (source: src/main/resources/application.yml:service-plan-domain-event-kafka-topic)
- Group: `${kafka.service-plan-domain-event.consumer.group}` (source: src/main/resources/application.yml:consumerGroupServicePlanDomainEvent)
- On each record calls `OfferedServicePlanPersistenceService.updateServicePlanNumber` to link offered plan with booked service plan (source: src/main/kotlin/com/maersk/iom/offer/messaging/ServicePlanDomainEventConsumer.kt:36)
