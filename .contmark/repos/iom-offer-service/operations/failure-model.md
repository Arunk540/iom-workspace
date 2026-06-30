---category: operations
title: Failure Model
summary: How iom-offer-service handles failures from external systems and internal errors
primary_for: [failure-model-reference, error-handling-guide]
mentions: [ErrorHandler, ExtendedExternalApiException, CustomsService, OfferClientWithMockFallback, RatesService, OfferedServicePlanPersistenceService]
scenarios:
  - understand failure handling for external calls
  - find how customs errors are handled
  - understand kafka consumer error handling
  - find what errors cause 502 response
  - understand circuit breaker open state behaviour
  - understand the failure model
capabilities: [offer-search, rate-calculation, kafka-consumption]
domains: [offer-management, rates]
entities: []
sources:
  - src/main/kotlin/com/maersk/iom/offer/webclient/ErrorHandler.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/CustomsService.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt
  - src/main/kotlin/com/maersk/iom/offer/messaging/ServicePlanDomainEventConsumer.kt
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [operations/retries.md, operations/monitoring.md, runtime/offer-search-flow.md]
---

## External API Error Handling

All WebClients use `ErrorHandler.handleExtendedExternalApiError()` which wraps any HTTP error response into `ExtendedExternalApiException` carrying the original status code (source: src/main/kotlin/com/maersk/iom/offer/webclient/ErrorHandler.kt:handleExtendedExternalApiError)

`ExtendedExternalApiException` propagates up the reactive chain and is surfaced to the caller as the appropriate HTTP status (typically 502 Bad Gateway for upstream failures).

Retry exhaustion also throws `ExtendedExternalApiException` (source: src/main/kotlin/com/maersk/iom/offer/webclient/ErrorHandler.kt:handleRetryExhausted)

## Per-System Failure Behaviour

| System | On Failure | Behaviour |
|---|---|---|
| Routing API | `ExtendedExternalApiException` | Retried 3×; circuit breaks; search fails |
| Offer API (prod) | `ExtendedExternalApiException` | Retried 3×; circuit breaks; search fails |
| Offer API (SIT/UAT) | Mock fallback active | Falls back to mock service; search continues (source: src/main/kotlin/com/maersk/iom/offer/webclient/offer/v3/OfferClient.kt:OfferClientWithMockFallback) |
| Customs API | Any error | Falls back to empty customs charges map; plan saved without customs charge (source: src/main/kotlin/com/maersk/iom/offer/service/v3/CustomsService.kt:getCustomsChargesWithFallback) |
| Cargo weight API | `ExtendedExternalApiException` | Retried; circuit breaks; search fails |
| Rates API | `ExtendedExternalApiException` | Retried 3×; `ValidationException` if mandatory charge mismatch (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt:validateAndConvertResponse) |
| MongoDB | Reactive error | Propagates; request fails with 500 |
| Charge type cache miss | Missing materialCode | `ExtendedExternalApiException(BAD_GATEWAY)` thrown (source: src/main/kotlin/com/maersk/iom/offer/service/v3/CustomsService.kt:getChargeTypeName) |
| productOffer null on merge | Data integrity issue | Logs error; skips charge persistence; returns existing data (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt:mergeAndPersist) |

## Kafka Consumer Error Handling

On processing failure: Kafka error handler retries up to `errorMaxBackoffRetry=3` times with `errorMaxBackoffInterval=100ms` exponential backoff. After exhaustion, the message is skipped and the error is logged (source: src/main/resources/application.yml:serviceplan-kafka-consumer-error)

Acknowledgement: `acknowledgementReqFlag=false` by default — Kafka uses automatic offset commit (source: src/main/kotlin/com/maersk/iom/offer/messaging/ServicePlanDomainEventConsumer.kt:28)

## Validation Failures

`ValidationException` from mandatory charge validation returns HTTP 400/422 to the caller with a list of `FieldValidationError` items containing the unexpected material codes (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt:validateAndConvertResponse)

`NotFoundException` when container IDs in rates request are not found in the offered plan returns HTTP 404 (source: src/main/kotlin/com/maersk/iom/offer/service/v3/RatesService.kt:validateContainerIds)
