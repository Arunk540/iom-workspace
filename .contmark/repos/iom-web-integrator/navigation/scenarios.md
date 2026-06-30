---
category: navigation
title: Scenarios to start-here map
summary: Task phrases map directly to the controller or service entry point that is most useful for debugging or extending the capability.
primary_for: [start-here-scenarios]
mentions: [navigation, triage, debug, start-here]
scenarios:
  - where to start
  - find start class
  - debug booking flow
  - trace offer path
  - trace preference bug
capabilities: [navigation]
domains: [Web Integrator]
entities: [Controller, Service]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/controller
  - src/main/kotlin/com/maersk/iom/webintegrator/service
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - navigation/key-classes.md
  - runtime/booking-creation-flow.md
---
# Scenarios to start-here map

| task phrase | start here |
| --- | --- |
| create ocean booking | `BookingController.createBooking` |
| amend booking payload | `BookingAmendmentService.amendBooking` |
| duplicate booking | `DuplicateAutoBookingService.duplicateBooking` |
| search booking offers | `OfferController.searchServicePlans` |
| amend offer search | `OfferController.searchServicePlansOnAmend` |
| manual reprice booking | `OfferController.repriceServicePlan` |
| bulk reprice bookings | `OfferController.searchBookingsForRepricing` |
| fetch service plan | `OrderController.getOrder` |
| update booking charges | `OrderController.updateCharges` |
| update booking containers | `OrderController.updateContainers` |
| close finops booking | `OrderController.updateSoftClosureStatus` |
| fetch business rules | `BusinessRuleController.getBusinessRules` |
| find optional parties | `BusinessRuleController.getOptionalParties` |
| fetch ccd contract | `CcdSearchController.getContractsByCustomerAndScope` |
| export finance documents | `ExportDocumentsController.exportDocuments` |
| trigger invoice | `InvoicingTriggerController.exportDocuments` |
| check outage banner | `NotificationController.checkExternalServices` |
| fetch user preferences | `UserPreferencesController.getUserPreferences` |
| update user preference | `UserPreferencesController.updateUserPreference` |
| ocean reference bootstrap | `OceanBookingService.getOceanBookingSearchResponseForTpDoc` |
