---category: domain
title: Equipment and cargo requirements
summary: Models the physical transport asset requirements for each container or vehicle, including reefer, dangerous-goods, and ownership details.
primary_for: [equipment-requirement-model]
mentions: [equipment profile, transport asset, reefer, dangerous goods]
scenarios:
  - equipment requirement model
  - reefer cargo details
  - dangerous goods shape
  - container asset profile
  - shipper owned container
  - find equipment requirements
capabilities: [equipment-modeling]
domains: [order-management]
entities: [TransportAssetRequirement, EquipmentProfile, DangerousGoods]
sources:
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/models/EquipmentProfile.kt
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/models/TransportAssetRequirement.kt
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/models/BookingEquipment.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - domain/booking.md
  - domain/product-offer.md
---
## Core shape

- `EquipmentProfile` normalizes size/type/height, tare and payload limits, construction material, and out-of-gauge suitability.
- `TransportAssetRequirement` points at a required equipment profile and adds reefer, genset, ventilation, atmosphere, humidity, hazardous-goods, and vehicle-specific attributes.
- `DangerousGoods` and its nested types capture IMDG, UNDG, package, measurement, and emergency-contact details.
- `BookingEquipment` carries one or more `TransportAssetRequirement` entries and exposes container-level execution state.

## Important behaviors

- `EquipmentProfile.getEquipmentSizeTypeCode()` gives the normalized ISO size-type code used by external masters.
- `BookingEquipment.executionStatus()` defaults to `NOT_STARTED` until a delivery execution work process exists.
- customs/VAS and automatic/manual charge handling live close to equipment because many pricing and execution rules operate per container.
