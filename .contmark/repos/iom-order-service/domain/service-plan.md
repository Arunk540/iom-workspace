---category: domain
title: ServicePlan domain
summary: `ServicePlan` is the central aggregate for booking, products, charges, legs, work-process state, and customer-history references. OrderServiceImpl enriches, validates, persists, and emits events around this aggregate.
primary_for: [service-plan-domain]
mentions: [service-plan, booking, charges, work-processes, domain-events]
scenarios:
  - inspect service plan rules
  - inspect status changes
  - inspect charge updates
  - inspect container updates
  - inspect repricing rules
  - understand the service plan
capabilities: [domain-model]
domains: [order]
entities: [ServicePlan, Booking, Charge, Reference, WorkProcess]
sources:
  - src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt
  - src/main/kotlin/com/maersk/iom/model/Extensions.kt
  - src/main/kotlin/com/maersk/iom/order/domain/config/CustomerDateDetailsProperties.kt
  - src/main/kotlin/com/maersk/iom/order/domain/config/CreatedBySystemChannelsProperties.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - domain/order-domain.md
  - runtime/order-creation-flow.md
  - contracts/db-schemas.md
---
- New service plans get generated `servicePlanNumber`, enriched charge IDs/soft-coded values, and `createdBy` derived from user or configured system channels before persistence.
- `save()` always adds `SERVICE_PLAN_CHANGED` and conditionally adds status, booking-status, and repricing domain events.
- `patch()` routes status patches through `updateServicePlanStatus`; non-status patches validate a transformed aggregate and resave it.
- `updateCharges()` copies user-supplied charges into the persisted aggregate, clears `finOpsActivitiesCompleted`, and refreshes the FinOps reference in booking refs.
- `updateContainers()` copies booking equipments, marks operation `CONTAINER_UPDATE`, validates, then persists.
- `updateOceanVesselDates()` rewrites ETA/ETD references and price-calculation date, then emits repricing when country/price-owner feature logic allows it.
- Customer-date configuration drives repricing windows and container-count aggregation.
