---
category: runtime
title: Order lifecycle flow
summary: Order lifecycle endpoints load, create, amend, update, and soft-close service plans while coordinating billing costs, vendor mapping, and haulage validation.
primary_for: [order-lifecycle-flow]
mentions: [order lifecycle, service plan update, soft close booking, finops update]
scenarios:
  - order lifecycle flow
  - service plan update flow
  - soft close flow
  - charges update flow
  - order status flow
capabilities: [order-lifecycle]
domains: [Orders]
entities: [ServicePlan, ServicePlanResponse, FinancialJobLine]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/OrderService.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/OrderClient.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/BillingClient.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - integrations/order-service.md
  - integrations/billing-service.md
---
# Order lifecycle flow

- Reads go through `OrderController.getOrder(...)` → `OrderService.getOrderWithCosts(...)`, which maps the downstream order payload and conditionally hydrates billing costs, draft costs, and manual invoice-trigger metadata. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:36)
- Create and amend calls both run haulage validation when the `iom-wi-haulage-validator-enabled` flag is on, then post a mapped `V3ServicePlanModel` to order service. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OrderService.kt:366)
- Charge updates enrich the service plan, push the main update to order service, then derive financial job lines and propagate non-empty billing deltas to billing service. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OrderService.kt:408)
- Vendor codes on cost lines are rewritten from SMDS to MDG codes before billing updates; a vendor lookup failure becomes `PartialUpdateException`. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OrderService.kt:418)
- Soft close first verifies finops completion or cancelled stage from order status, then checks that financial-job-closure milestone is not already completed before patching billing. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OrderService.kt:607)
- Order-side transport hits `/search`, `/multi-search`, `/containers`, `/status`, `/{servicePlanNumber}`, `/{servicePlanNumber}/charges`, `/{servicePlanNumber}/containers`, and related order endpoints via `OrderClient`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/OrderClient.kt:176)
