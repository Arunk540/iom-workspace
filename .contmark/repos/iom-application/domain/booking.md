---
category: domain
title: Booking subtree
summary: Captures the customer-facing booking state nested inside ServicePlan, including parties, equipment, references, work processes, and charges.
primary_for: [booking-subtree-model]
mentions: [booking model, work processes, booking charges, party roles]
scenarios:
  - booking domain model
  - where booking lives
  - booking work process
  - booking charges logic
  - price owner lookup
capabilities: [booking-modeling]
domains: [order-management]
entities: [Booking, BookingEquipment, WorkProcess]
sources:
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/models/Booking.kt
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/models/BookingEquipment.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - domain/service-plan.md
  - domain/party.md
  - domain/equipment.md
---
## Core shape

`Booking` owns:

- booking identity and status
- parties and booking equipments
- receive channel, references, instructions, standard reasons
- work-process timelines and charges
- soft-coded values, business exceptions, and instant-confirm markers

`BookingEquipment` extends the booking subtree with:

- commodity and cargo details
- transport asset requirements
- container legs and equipment-level charges
- execution charges, VAS, customs flags, and execution work processes

## Important behaviors

- booking-level `deleteCharges()` and equipment-level `deleteCharges()` disable most charges during cancellation
- `priceOwner()` locates the party association with `PRICE_OWNER`
- work-process helpers expose states such as executed, send-to-TMS in progress, ready-for-planning completed, and invoice-trigger completed
- booking-equipment helpers merge manual/execution charges and mark containers deleted without deleting the whole aggregate
