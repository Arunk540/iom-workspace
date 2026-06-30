---
category: contracts
title: API contracts — booking, offers, and rules
summary: Inbound HTTP contracts for booking creation, offer search, rules, notification, finance documents, invoicing, and CCD lookup.
primary_for: [api-contracts-booking]
mentions: [api, contracts, booking, offers, rules]
scenarios:
  - booking contracts
  - booking api contract
  - offer contracts
  - rules contracts
  - search contract list
capabilities: [api-contracts]
domains: [Web Integrator]
entities: [BookingRequest, ServicePlan, BusinessRuleRequest]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/controller
  - src/main/resources/openapi/iom-web-integrator-openapi-spec-v1.yaml
  - src/main/kotlin/com/maersk/iom/webintegrator/model/dto
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - contracts/api-contracts-2.md
  - navigation/entry-points-1.md
---
# API contracts — part 1

- `POST /bookings` — `BookingRequest` body → `BookingResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/BookingController.kt:33)
- `POST /business-rules` — `BusinessRuleRequest` body → `BusinessRuleResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/BusinessRuleController.kt:26)
- `GET /business-rules/optional-parties` — no body → `BusinessRuleResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/BusinessRuleController.kt:37)
- `GET /ccd-search/contracts/customer/{customerCode}/scope/{scope}` — path params + `Authorization` → `CcdSearchResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/CcdSearchController.kt:24)
- `POST /config-rules` — `ConfigRuleRequest` body → raw JSON string. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/ConfigController.kt:23)
- `POST /finance/export-documents` — `ExportDocumentsRequest(documents[])` → `ExportDocumentsResponse(documentsExport[])`. (source: src/main/kotlin/com/maersk/iom/webintegrator/model/dto/ExportDocumentsRequest.kt:3)
- `POST /service-plans/{servicePlanNumber}/incident` — `Incident` body + path variable → `IncidentResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/IncidentController.kt:19)
- `POST /trigger-invoice` — `InvoiceTriggerRequest` body → `InvoiceTriggerResponse`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/InvoicingTriggerController.kt:22)
- `GET /notification` — no body → `ResponseEntity<String>` with 200/503/500 semantics. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/NotificationController.kt:31)
- `POST /service-plans-queries` — `SearchRequest` body → `Flux<ServicePlan>`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt:62)
- `GET /service-plans-queries/service-plans/{servicePlanNumber}` — path variable + optional `duplicate` → `SearchRequest`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt:207)
- `PUT /service-plans-queries` — `SearchRequest` body → `Flux<ServicePlan>`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt:217)
- `PATCH /service-plans-queries/service-plans/{servicePlanNumber}/reprice` — `RepriceRequest` body → `ServicePlan`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt:284)
- `POST /service-plans-queries/reprice` — `RepricingSearchRequest` body → `RepricingSearchResponseContent`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/OfferController.kt:313)
- `GET /service-plans-queries/ocean-reference/{oceanReferenceNumber}` — path variable → `OceanBookingSearchResponse`; path is defined in the OpenAPI spec. (source: src/main/resources/openapi/iom-web-integrator-openapi-spec-v1.yaml:185)
- `GET /service-plans/search` is not exposed; dashboard search is explicitly `POST /service-plans/search` with pagination query params. (source: src/main/resources/openapi/iom-web-integrator-openapi-spec-v1.yaml:206)
