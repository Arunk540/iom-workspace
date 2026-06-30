---
category: domain
title: BookingRequest bootstrap input
summary: `BookingRequest` is the lightweight bootstrap payload used only by `/bookings` to decide whether to create, duplicate, or amend a booking.
primary_for: [booking-request-shape]
mentions: [bookingrequest, autobooking, duplicate, amendment]
scenarios:
  - booking request fields
  - ocean booking payload
  - duplicate booking payload
  - amend booking payload
  - booking bootstrap
capabilities: [domain-model]
domains: [Booking]
entities: [BookingRequest, Address, BookingResponse]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/model/dto/booking/Booking.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/controller/BookingController.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - runtime/booking-creation-flow.md
  - domain/ocean-booking.md
---
# BookingRequest bootstrap input

- Fields include `oceanBookingReference`, pickup/delivery dates, delivery address, terminal, transport activity, duplicate/amendment flags, optional parties, customs flags, and value-added services. (source: src/main/kotlin/com/maersk/iom/webintegrator/model/dto/booking/Booking.kt:8)
- `locationFunction()` chooses `CONTAINER_STUFFING_LOCATION` for exports and `PLACE_OF_DELIVERY` for imports. (source: src/main/kotlin/com/maersk/iom/webintegrator/model/dto/booking/Booking.kt:29)
- `BookingController.createBooking(...)` switches on `isAmendment`, `duplicateBooking`, and non-blank `oceanBookingReference` to pick the bootstrap path. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/BookingController.kt:39)
