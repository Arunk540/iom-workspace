---
category: runtime
title: Repricing flow
summary: Manual and bulk repricing both compute a repriced offer, diff mandatory/base charges against the existing service plan, then persist updated charges through order service.
primary_for: [repricing-flow]
mentions: [repricing, price update, bulk reprice, manual reprice]
scenarios:
  - repricing flow
  - repricing debug
  - repricing charges
  - manual repricing
  - bulk repricing
capabilities: [repricing]
domains: [Pricing]
entities: [RepriceRequest, RepricingSearchRequest, Charge]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/OfferService.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/OrderService.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - integrations/offer-service.md
  - integrations/order-service.md
---
# Repricing flow

- Manual repricing enters through `PATCH /service-plans-queries/service-plans/{servicePlanNumber}/reprice`, fetches the existing search request, computes a repriced offer, persists updated charges through `OrderService.updateCharges(...)`, and returns the repriced service plan. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt:284)
- `OfferService.getRepricedOffer(...)` calls the offer-service repricing endpoint using the offered service plan number plus price-calculation context. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OfferService.kt:298)
- `OfferService.updateServicePlanWithRepricedCharges(...)` diffs grouped mandatory/base charges, rejects no-op repricing with `NoAppropriateDataFoundException`, then rewrites charge IDs and standard reasons on the existing aggregate. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OfferService.kt:249)
- Bulk repricing search enters through `POST /service-plans-queries/reprice`, searches the first page of bookings, reprices each one concurrently, and emits partial rows when one booking fails. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt:313)
- Batch application of repriced charges enters through `POST /service-plans/reprice`, which fetches current orders and offered service plans per selection before updating charges one by one. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:294)
