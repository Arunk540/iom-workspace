---
category: navigation
title: Entry points — booking, offer, and search APIs
summary: First half of the inbound REST surface, covering booking creation, offer search, business rules, notifications, invoicing, and CCD lookups.
primary_for: [entry-point-map]
mentions: [controllers, rest, booking, offers, search, notification]
scenarios:
  - booking endpoint map
  - offer endpoint map
  - search api map
  - find rest handler
  - where request enters
capabilities: [http-entrypoints]
domains: [Web Integrator]
entities: [BookingRequest, ServicePlan, BusinessRuleRequest]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/controller
  - src/main/resources/openapi/iom-web-integrator-openapi-spec-v1.yaml
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - navigation/entry-points-2.md
  - navigation/scenarios.md
---
# Entry points — part 1

- `POST /bookings` → `BookingController.createBooking`; branches to amendment, duplicate, or ocean auto-booking before persisting through `OrderService.createOrder`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/BookingController.kt:33)
- `POST /business-rules` → `BusinessRuleController.getBusinessRules`; accepts `Mono<BusinessRuleRequest>` and returns `Mono<BusinessRuleResponse>`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/BusinessRuleController.kt:26)
- `GET /business-rules/optional-parties` → `BusinessRuleController.getOptionalParties`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/BusinessRuleController.kt:37)
- `GET /ccd-search/contracts/customer/{customerCode}/scope/{scope}` → `CcdSearchController.getContractsByCustomerAndScope`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/CcdSearchController.kt:24)
- `POST /config-rules` → `ConfigController.getConfigValues`; returns LaunchDarkly JSON for customer/country. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/ConfigController.kt:23)
- `POST /finance/export-documents` → `ExportDocumentsController.exportDocuments`; wraps internal export-document DTOs. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/ExportDocumentsController.kt:21)
- `POST /service-plans/{servicePlanNumber}/incident` → `IncidentController.createIncident`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/IncidentController.kt:19)
- `POST /trigger-invoice` → `InvoicingTriggerController.exportDocuments`; forwards invoice-trigger requests to billing. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/InvoicingTriggerController.kt:22)
- `GET /notification` → `NotificationController.checkExternalServices`; health-gates the notification banner payload. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/NotificationController.kt:31)
- `POST /service-plans-queries` → `OfferController.searchServicePlans`; offer search for new bookings. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt:62)
- `GET /service-plans-queries/service-plans/{servicePlanNumber}` → `OfferController.getSearchRequest`; reconstructs a `SearchRequest` from an existing service plan. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt:207)
- `PUT /service-plans-queries` → `OfferController.searchServicePlansOnAmend`; offer search for amendment flows. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt:217)
- `PATCH /service-plans-queries/service-plans/{servicePlanNumber}/reprice` → `OfferController.repriceServicePlan`; recomputes charges and persists them through order service. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt:284)
- `POST /service-plans-queries/reprice` → `OfferController.searchBookingsForRepricing`; bulk repricing search. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt:313)
- `GET /service-plans-queries/ocean-reference/{oceanReferenceNumber}` → `OfferController.getSearchRequestByOceanReference`; bootstrap search data from ocean reference. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt:445)
- `GET /service-plans/{servicePlanNumber}` → `OrderController.getOrder`; supports `includeDraftRevenues` and `includeCosts`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:36)
- `POST /service-plans/search` → `OrderController.getServicePlans`; paged dashboard search. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:54)
- `POST /service-plans/multi-search` → `OrderController.getServicePlansMatchingMultiQuery`; multi-reference dashboard search. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OrderController.kt:69)

## Notes

- The OpenAPI spec at this SHA advertises 36 operations, so the controller surface is larger than the older “24 entry points” note in task framing. (source: src/main/resources/openapi/iom-web-integrator-openapi-spec-v1.yaml:23)
