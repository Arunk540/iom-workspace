---
category: runtime
title: Offer Cleanup Flow
summary: Scheduled deletion of unmapped offered service plan documents
primary_for: [offer-cleanup-runtime-flow, stale-record-deletion-trace]
mentions: [OfferServiceCleanUpController, OfferRecordCleanUpService, ReactiveMongoTemplate]
scenarios:
  - understand how stale offers are deleted
  - find cleanup query logic
  - trace delete offered plan endpoint
  - understand what unmapped means
  - find how old records are identified
capabilities: [offer-cleanup, persistence]
domains: [offer-management]
entities: [OfferedServicePlanDocument, OfferRecordsDeleteResponse]
sources:
  - src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceCleanUpController.kt
  - src/main/kotlin/com/maersk/iom/offer/service/v3/OfferRecordCleanUpService.kt
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [runtime/service-plan-event-processing-flow.md, contracts/db-schemas.md, operations/failure-model.md]
---

## Entry Point

`DELETE /v3/offered-service-plan?numberOfDays=N` — `@PreAuthorize("permitAll()")` — no JWT required (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceCleanUpController.kt:34)

Delegates to `OfferRecordCleanUpService.removeUnmappedOffers(numberOfDays)` (source: src/main/kotlin/com/maersk/iom/offer/controller/v3/OfferServiceCleanUpController.kt:44)

## Cleanup Logic

`OfferRecordCleanUpService.removeUnmappedOffers(numberOfDays: Long)` (source: src/main/kotlin/com/maersk/iom/offer/service/v3/OfferRecordCleanUpService.kt:removeUnmappedOffers):

1. Compute `cutoffTimestamp = now - numberOfDays` (epoch millis at UTC midnight)
2. Build MongoDB criteria:
   ```
   { createdTime: { $lt: cutoffTimestamp }, servicePlanNumber: null }
   ```
3. Execute `ReactiveMongoTemplate.find(query, OfferedServicePlanDocument::class.java, "offered_service_plans")` — collect matching document IDs and log them
4. Execute `ReactiveMongoTemplate.remove(query, "offered_service_plans")` — delete all matching documents
5. Return `OfferRecordsDeleteResponse(numberOfRecords = result.deletedCount)`

## Why "Unmapped"

"Unmapped" means `servicePlanNumber IS NULL` — the booking system has not yet confirmed a service plan for this offered plan. After `ServicePlanDomainEventConsumer` processes the Kafka event, `servicePlanNumber` is populated and the record is no longer eligible for cleanup.

## Criteria Summary

| Criterion | Field | Condition |
|---|---|---|
| Age | `createdTime` | `< cutoff timestamp (epoch ms)` |
| Unmapped | `servicePlanNumber` | `IS NULL` |

Both criteria are required (`andOperator`) (source: src/main/kotlin/com/maersk/iom/offer/service/v3/OfferRecordCleanUpService.kt:18)

## Response

`OfferRecordsDeleteResponse(numberOfRecords: Long)` — count of deleted records. Returns 0 if no matching documents found (source: src/main/kotlin/com/maersk/iom/offer/service/v3/OfferRecordCleanUpService.kt:30)

## Operational Notes

- No automatic scheduling — must be called externally (e.g., cron job or CI pipeline)
- No dry-run mode; deletion is immediate
- Logs document IDs before deletion for audit trail (source: src/main/kotlin/com/maersk/iom/offer/service/v3/OfferRecordCleanUpService.kt:24)
