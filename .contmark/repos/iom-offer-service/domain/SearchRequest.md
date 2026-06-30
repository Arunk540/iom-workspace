---category: domain
title: SearchRequest
summary: Internal domain object representing a validated, enriched inland offer search request
primary_for: [search-request-domain-model, offer-search-input-contract]
mentions: [IOMRoutingAndOfferService, SearchRequestService, V3SearchRequest, SearchRequestDocument, V3RequestMapper]
scenarios:
  - understand search request structure
  - add new field to search request
  - trace search input to service
  - find transport activity field meaning
  - understand booking equipment structure
capabilities: [offer-search, request-enrichment]
domains: [offer-management, routing]
entities: [SearchRequest, ContainerRequest, ContainerDetail, TimeRangeRequest]
sources:
  - src/main/kotlin/com/maersk/iom/offer/domain/SearchRequest.kt
  - src/main/kotlin/com/maersk/iom/offer/mapper/V3RequestMapper.kt
verified_against: 7e42b470a171e83034f5b87317665f282611a72d
last_updated: "2026-06-30"
related: [domain/OfferRequest.md, domain/OfferedServicePlanDocument.md, runtime/offer-search-flow.md]
---

## Overview

`SearchRequest` is the internal domain representation of an offer search input. It is mapped from `V3SearchRequest` (the inbound DTO) via `V3RequestMapper.toDomain()` and persisted as `SearchRequestDocument` in the `search_request` MongoDB collection.

## Data Class Fields (`SearchRequest`)

| Field | Type | Description |
|---|---|---|
| `origin` | `LocationAssociation` | Origin facility/city |
| `destination` | `LocationAssociation` | Destination facility/city |
| `emptyContainerPoint` | `LocationAssociation?` | Optional empty container pickup |
| `parties` | `List<PartyAssociation>` | Booker, payer, credit party |
| `bookingEquipments` | `List<ContainerRequest>` | One entry per equipment group |
| `timeRange` | `TimeRangeRequest?` | Earliest departure window |
| `transportActivity` | `String` | `EXPORT` or `IMPORT` |
| `servicePlanNumber` | `String?` | Populated for amend flow |
| `searchRequestId` | `String?` | UUID assigned on persist |
| `isCustomerJourney` | `Boolean` | Customer-facing flag |
| `isCarrierHaulage` | `Boolean?` | Carrier haulage indicator |
| `isScmBooking` | `Boolean?` | SCM booking flag |
| `oceanReferenceNumber` | `String?` | Linked ocean booking |
| `preferredTransportMode` | `String?` | Override routing condition |

## ContainerRequest Fields

| Field | Type | Description |
|---|---|---|
| `commodities` | `List<Commodity>?` | HS code and commodity info |
| `equipmentProfile` | `EquipmentProfile` | ISO size/type (e.g., 20DR, 40HC) |
| `expectedNetWeight` | `BigDecimal?` | Net cargo weight |
| `quantity` | `Int` | Number of containers |
| `cargoType` | `String` | FCL/etc |
| `bookingEquipmentIdentifier` | `String?` | Unique equipment ID within offer |
| `isNonOperatingReefer` | `Boolean?` | NOR flag |
| `temperature` / `atmosphere` / `humidity` | Optional | Reefer settings |
| `dangerousGoods` | `List<DangerousGoods>?` | DG details |
| `hasCustoms` | `Boolean?` | Whether customs charge should be fetched |
| `containerDetails` | `List<ContainerDetail>` | Execution status per container |

## Lifecycle

1. Inbound `V3SearchRequest` → `enrichSearchRequest()` (adds location data, validates commodity/container-type)
2. `toDomain()` mapper converts to `SearchRequest`
3. `SearchRequestService.persistSearchRequest()` saves as `SearchRequestDocument`; returns with `searchRequestId`
4. `SearchRequest.searchRequestId` is used to link `OfferedServicePlanDocument.searchRequestId` (source: src/main/kotlin/com/maersk/iom/offer/domain/SearchRequest.kt:12)
