---
category: integrations
title: Offer and routing services
summary: Explains how the library requests offered service plans and routing options from external systems using local DTO contracts.
primary_for: [offer-routing-integration]
mentions: [offered service plan, routing client, route lookup, offer client]
scenarios:
  - find routing integration
  - trace offer lookup
  - which dto drives routing
  - how route api called
  - where offer maps to domain
capabilities: [integration-mapping]
domains: [reference-data]
entities: [OfferedServicePlanClientV3, RoutingClient, V2RoutingRequestModel]
sources:
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/config/WebClientConfiguration.kt
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/offer/OfferClient.kt
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/routing/RoutingClient.kt
  - application-model/src/main/kotlin/com/maersk/iom/application/model/dto/v2/routing/V2RoutingRequestModel.kt
  - application-model/src/main/kotlin/com/maersk/iom/application/model/dto/v2/offer/V2OfferRequestModel.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - contracts/api-contracts.md
  - runtime/order-processing.md
---
## Offered service plans

- `WebClientConfiguration.offeredServicePlanClient()` creates an OAuth2-backed client for `services.iomofferservice.base-url`. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/config/WebClientConfiguration.kt:136)
- `OfferedServicePlanClientImpl.getOfferedServicePlan()` performs a GET using the offered service-plan number, deserializes `V3ServicePlanModel`, and maps it into the internal domain `ServicePlan`. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/offer/OfferClient.kt:20)

## Routing

- `WebClientConfiguration.routingClient()` builds the routing client with the IRE OAuth registration plus the routing consumer key. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/config/WebClientConfiguration.kt:270)
- `RoutingClientImpl.getRoutings()` POSTs a `V2RoutingRequestModel`, adds `page` and `limit` query params, and sends `Api-version` and `correlation-id` headers before deserializing `V2RoutingResponseModel`. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/routing/RoutingClient.kt:22)
- The local routing request contract includes start/end locations, cargo, carriage, equipment, named accounts, and route selection hints, while `V2OfferRequestModel` packages selected routing plus commodity/container pricing input. (source: application-model/src/main/kotlin/com/maersk/iom/application/model/dto/v2/routing/V2RoutingRequestModel.kt:12) (source: application-model/src/main/kotlin/com/maersk/iom/application/model/dto/v2/offer/V2OfferRequestModel.kt:9)
