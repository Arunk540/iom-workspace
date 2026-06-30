---
category: runtime
title: Booking creation flow
summary: `/bookings` is a bootstrap endpoint that selects amendment, duplication, or ocean-reference creation before normalizing the service plan and posting it to order service.
primary_for: [booking-creation-flow]
mentions: [booking flow, autobooking, duplicate booking, amend booking]
scenarios:
  - booking create flow
  - duplicate booking flow
  - amend booking flow
  - ocean booking flow
  - create booking trace
capabilities: [booking-create]
domains: [Booking]
entities: [BookingRequest, ServicePlan, BookingResponse]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/controller/BookingController.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/BookingAmendmentService.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/DuplicateAutoBookingService.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/OceanAutoBookingService.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/OrderService.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - domain/booking-request.md
  - runtime/ocean-booking-enrichment-flow.md
---
# Booking creation flow

- `BookingController.createBooking(...)` receives `BookingRequest` plus bearer token and chooses one of three branches: amend existing booking, duplicate existing booking, or ocean-reference booking creation. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/BookingController.kt:33)
- Amendment branch calls `BookingAmendmentService.amendBooking(...)`, which loads the existing order and either swaps the customer facility or updates delivery-date haulage nodes. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/BookingAmendmentService.kt:16)
- Duplicate branch calls `DuplicateAutoBookingService.duplicateBooking(...)`, rebuilds a search request from the existing order, re-searches offers, then updates haulage pickup/delivery dates. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/DuplicateAutoBookingService.kt:19)
- Ocean-reference branch calls `OceanAutoBookingService.createBooking(...)`, which turns TP-doc data into a search request, finds customer facility/location context, and returns a service plan candidate. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OceanAutoBookingService.kt:21)
- After branch selection, `BookingController.updateServicePlan(...)` injects UTC-normalized haulage dates, receive channel, credit/bill-to parties, customs references, and value-added services. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/BookingController.kt:53)
- The flow persists through `OrderService.createOrder(...)`, which runs optional haulage validation before calling `OrderClient.createOrder(...)`. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/OrderService.kt:513)
