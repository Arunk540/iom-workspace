---
category: domain
title: Party model
summary: Defines the reusable party, role-relationship, and payment structures that travel through bookings, charges, and resolver output.
primary_for: [party-domain-model]
mentions: [party association, customer party, booked by, price owner]
scenarios:
  - party domain model
  - customer role structure
  - where parties stored
  - payment term model
  - party relationship fields
capabilities: [party-modeling]
domains: [order-management]
entities: [PartyAssociation, Party, PaymentCondition]
sources:
  - iom-order-domain/src/main/kotlin/com/maersk/iom/order/domain/models/Party.kt
  - iom-common/src/main/kotlin/com/maersk/iom/common/enums/IOMEnums.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - domain/booking.md
  - operations/flags-and-lists.md
  - integrations/customer-and-facility.md
---
## Core shape

- `PartyAssociation` is a thin wrapper around `Party` so the same party structure can appear in bookings, charges, and other nested lists.
- `Party` stores `partyCode`, `partyMasterRole`, telecom numbers, email addresses, role relationships, postal addresses, names, business entity codes, payment condition, alternative codes, and SCV identifiers.
- `PaymentCondition` and `PaymentTerm` keep payment semantics close to the party model.

## Why it matters

- booking logic uses party roles to find the `PRICE_OWNER`
- DTO annotations and validators treat some roles specially, such as `BOOKED_BY_CONTACT`
- resolvers enrich these party records from the customer service before validation and domain execution
