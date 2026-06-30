---category: contracts
title: API contracts
summary: Describes the versioned DTO surfaces and custom validation annotations that this library publishes for consumers.
primary_for: [versioned-api-contracts]
mentions: [dto contracts, v1, v2, v3, validation annotation]
scenarios:
  - api dto versions
  - find request contract
  - which dto version
  - routing request model
  - postal address annotation
  - find the api contracts
  - check contracts definitions
capabilities: [contract-mapping]
domains: [api-contracts]
entities: [V1ServicePlanModel, V2RoutingRequestModel, V3ServicePlanModel]
sources:
  - application-model/src/main/kotlin/com/maersk/iom/application/model/dto/v1/V1ServicePlanModel.kt
  - application-model/src/main/kotlin/com/maersk/iom/application/model/dto/v1/V1BookingModel.kt
  - application-model/src/main/kotlin/com/maersk/iom/application/model/dto/v2/routing/V2RoutingRequestModel.kt
  - application-model/src/main/kotlin/com/maersk/iom/application/model/dto/v2/offer/V2OfferRequestModel.kt
  - application-model/src/main/kotlin/com/maersk/iom/application/model/dto/v3/V3ServicePlanModel.kt
  - application-model/src/main/kotlin/com/maersk/iom/application/annotation/PostalAddress.kt
  - application-model/src/main/kotlin/com/maersk/iom/application/annotation/Duration.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - navigation/entry-points.md
  - contracts/db-schemas.md
---
## Versioned DTO surfaces

- `V1ServicePlanModel` exposes the legacy service-plan request shape with `booking`, `servicePlanLegs`, `documentPouch`, `productOffer`, `cargoServiceType`, `serviceTypeModes`, and `servicePlanStage`. (source: application-model/src/main/kotlin/com/maersk/iom/application/model/dto/v1/V1ServicePlanModel.kt:11)
- `V1BookingModel` adds the legacy booking sub-contract for parties, booking equipments, receive channel, standard reasons, references, weights, instructions, and work processes. (source: application-model/src/main/kotlin/com/maersk/iom/application/model/dto/v1/V1BookingModel.kt:13)
- `V2RoutingRequestModel` is the local contract for routing lookups, carrying request type, start/end locations, transport activity, cargo, carriage, equipment, named accounts, and route selection hints. (source: application-model/src/main/kotlin/com/maersk/iom/application/model/dto/v2/routing/V2RoutingRequestModel.kt:12)
- `V2OfferRequestModel` is the local offer request contract, combining origin/destination, commodity, containers, shipment pricing date, routing list, and transport metadata. (source: application-model/src/main/kotlin/com/maersk/iom/application/model/dto/v2/offer/V2OfferRequestModel.kt:9)
- `V3ServicePlanModel` is the richest current contract, requiring `offeredServicePlanNumber` and carrying booking, product offer, service type modes, restrictions, finops/customs flags, routing, order type, truck type, and free-days data. (source: application-model/src/main/kotlin/com/maersk/iom/application/model/dto/v3/V3ServicePlanModel.kt:30)

## Annotation-based constraints

- `@PostalAddress` validates `V3PartyModel` so parties other than `BOOKED_BY_CONTACT` must keep postal addresses populated. (source: application-model/src/main/kotlin/com/maersk/iom/application/annotation/PostalAddress.kt:11)
- `@Duration` validates ISO-8601 duration strings by calling `java.time.Duration.parse` and rejecting null or malformed values. (source: application-model/src/main/kotlin/com/maersk/iom/application/annotation/Duration.kt:11)
