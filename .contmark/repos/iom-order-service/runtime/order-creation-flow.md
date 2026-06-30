---
category: runtime
title: Order creation flow
summary: The primary write flow starts in `V3OrderController.update` and converges on `OrderServiceImpl.createOrUpdateOrder`, which resolves external data, validates, persists, and optionally auto-confirms. All write endpoints reuse the same save/event pipeline after shaping the aggregate.
primary_for: [order-creation]
mentions: [v3-order-controller, create-update, validators, save-pipeline, auto-confirm]
scenarios:
  - trace create order
  - trace patch order
  - trace amend order
  - trace charge update
  - trace container update
capabilities: [runtime-flow]
domains: [order]
entities: [V3ServicePlanModel, ServicePlan, ResolvedData, FrnUpdateRequest]
sources:
  - src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt
  - src/main/kotlin/com/maersk/iom/mapper/V3DomainMapper.kt
  - src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt
  - src/main/kotlin/com/maersk/iom/service/ExecutionChargeService.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - domain/service-plan.md
  - contracts/api-contracts.md
  - integrations/telikos-booking.md
---
1. `V3OrderController.update` builds `RequestContextProvider`, enriches booked-by parties, maps `V3ServicePlanModel` to domain, and calls `orderService.createOrUpdateOrder(...)`. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:64-84)
2. `V3ServicePlanStatusSearchQuery`, `V3PatchRequestModel`, and `V3ServicePlanMultiSearchRequest` map into domain request objects in `V3DomainMapper`; patch requests always build a domain `PatchRequest` with the selected payload branch. (source: src/main/kotlin/com/maersk/iom/mapper/V3DomainMapper.kt:11-31, src/main/kotlin/com/maersk/iom/mapper/V3DomainMapper.kt:34-107)
3. `OrderServiceImpl.createOrUpdateOrder` resolves external/master data, optionally enriches customer-journey parties, resets sensitive values from persisted state or defaults, validates basic/manual-pricing rules, validates SAP/TMS amendment rules, and persists via `save(...)`. (source: src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:457-506)
4. `save(...)` enriches IDs/charges/creator, adds planning deadline, handles customer-order business exceptions, saves the aggregate, and dispatches outbox-backed domain events. (source: src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:180-225, src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:232-244, src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:323-349)
5. After create/update, the domain may auto-book on single-click confirm by setting `BOOKED`, mirroring booking status, saving again, and firing status-change events. (source: src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:487-504)
6. Related write endpoints all reuse the same domain service: patch→`patch`, amend→`amendOrder`, charges→`updateCharges`, containers→`updateContainers`, vessel dates→`updateOceanVesselDates`, FRN→`updateFrn`, FinOps completion→`updateFinOpsActivityCompletedStatus`. (source: src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:86-149, src/main/kotlin/com/maersk/iom/controller/v3/V3OrderController.kt:150-338)
7. Execution charges take a side path: `ExecutionChargesController` delegates to `ExecutionChargeService`, which loads the order, maps request charges, mutates execution charges, and calls `orderService.save(...)`. (source: src/main/kotlin/com/maersk/iom/controller/v3/ExecutionChargesController.kt:64-89, src/main/kotlin/com/maersk/iom/service/ExecutionChargeService.kt:20-43)
