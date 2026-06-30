---category: domain
title: Product offer and charges
summary: Represents the priced product tree selected for a service plan, including products, service-plan legs, equipment, and manual/automatic charges.
primary_for: [product-offer-model]
mentions: [priced products, charges, product legs, execution charges]
scenarios:
  - product offer model
  - merge charges logic
  - pricing structure
  - execution charges
  - all charges traversal
  - find the product offer
capabilities: [pricing-modeling]
domains: [order-management]
entities: [ProductOffer, Product, Charge]
sources:
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/models/ProductOffer.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - domain/service-plan.md
  - domain/equipment.md
  - operations/failure-model.md
---
## Core shape

- `ProductOffer` wraps selected `products`, product-offer-level `charges`, and `executionCharges`.
- Each `Product` carries `servicePlanLegs`, `bookingEquipments`, VAS, and haulage details.
- `Charge` stores parties, rate basis, currency amounts, references, soft-coded values, deletion flags, and classification fields.

## Important behaviors

- `mergeCharges()` and `mergeExecutionCharges()` reconcile additions, updates, and soft deletes.
- `getAllCharges()` traverses product-offer, product, leg, and equipment levels into one flat list.
- `hasCustoms()`, `getEquipmentLevelChargesByType()`, and `updateStaleCharges()` support downstream pricing, execution, and amendment logic.
- `Charge.applyCancellation()` preserves the cancellation-fee code while deactivating most other charges.
