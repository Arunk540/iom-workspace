---
category: domain
title: ServicePlan aggregate
summary: The root order aggregate links booking, product offer, routing, stage, customs, finance, and event-facing metadata.
primary_for: [service-plan-aggregate]
mentions: [order aggregate, service plan status, routing, finops, customs]
scenarios:
  - service plan aggregate
  - order root model
  - amend service plan
  - service plan fields
  - cancel logic
capabilities: [domain-modeling]
domains: [order-management]
entities: [ServicePlan, ServicePlanLeg, ServicePlanStatus]
sources:
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/models/ServicePlan.kt
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/models/ServicePlanLeg.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - domain/booking.md
  - domain/product-offer.md
  - runtime/order-processing.md
---
## Core shape

`ServicePlan` is the aggregate root for the library domain. It combines:

- identity: `servicePlanNumber`, `offeredServicePlanNumber`
- commercial state: `booking`, `productOffer`, `serviceTypeModes`, `orderType`
- execution state: `servicePlanStage`, `hasAmendment`, `isAutomatedExecution`, `portGateInOrOut`
- finance/customs state: `finOpsActivitiesCompleted`, `finOpsPendingActions`, `hasVatPartner`, `hasCustoms`, `customsStatus`
- routing state: `routing`, `transportMode`, `cargoCity`, `pickupFreeDays`, `deliveryFreeDays`

## Important behaviors

- cancellation checks depend on stage, product/leg presence, intermodal direction, and cargo-moving timestamps
- status transitions use `changeStatus`, `isBooked`, `isDraft`, `canAmend`, and `canCancel`
- the aggregate exposes compatibility constructors for earlier shapes that still carry `servicePlanLegs`
- `legs()` style traversal ultimately delegates to product/service-plan leg structures

## Related types

- `ServicePlanLeg` holds start/end locations, event-triggered service dates, optional charges, and VAS lists.
- `ServicePlanStatus` is the shared state machine enum for draft/booked/confirmed/cancelled-style transitions.
- `DomainEventFactory` turns a `ServicePlan` into change, status-change, booking-status, and repricing events.
