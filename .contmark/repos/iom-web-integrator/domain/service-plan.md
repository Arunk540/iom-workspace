---
category: domain
title: ServicePlan aggregate
summary: `ServicePlan` is the central API aggregate; offer, order, billing, and repricing flows all transform or enrich this model.
primary_for: [service-plan-aggregate]
mentions: [serviceplan, charges, haulage, booking, repricing]
scenarios:
  - service plan model
  - service plan charges
  - service plan haulage
  - repricing service plan
  - booking aggregate
capabilities: [domain-model]
domains: [Booking]
entities: [ServicePlan, Charge, BookingEquipment]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/mapper/V3ServicePlanMapper.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/OrderService.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/OfferService.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - runtime/order-lifecycle-flow.md
  - runtime/repricing-flow.md
---
# ServicePlan aggregate

- `V3ServicePlanMapper.toBffModel(...)` turns downstream order/offer payloads into the BFF `ServicePlan` model and populates booking, booking equipment, charges, haulage, documents, and restrictions. (source: src/main/kotlin/com/maersk/iom/webintegrator/mapper/V3ServicePlanMapper.kt:55)
- `OrderService.getOrderWithCosts(...)` optionally enriches a service plan with billing costs, draft costs, and manual-invoice-trigger flags. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OrderService.kt:77)
- `OfferService.updateServicePlanWithRepricedCharges(...)` compares existing vs repriced mandatory/base charges and rewrites charge IDs on the same booking equipment identifiers. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OfferService.kt:249)
- `OrderController.updateCharges(...)` adds a `PRICE_UPDATE` standard reason if it is missing before calling the service update flow. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:165)
