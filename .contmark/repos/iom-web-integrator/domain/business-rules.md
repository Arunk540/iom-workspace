---
category: domain
title: Business rules response model
summary: Business-rule evaluation combines mandatory parties, mandatory documents/references, optional lists, and Project44 onboarding state.
primary_for: [business-rules-model]
mentions: [businessrules, mandatory, optional, project44]
scenarios:
  - business rules payload
  - mandatory parties rules
  - optional documents list
  - project44 customer rules
  - rule evaluation model
capabilities: [domain-model]
domains: [Compliance]
entities: [BusinessRuleResponse, MandatoryPartyRequest, MandatoryDocumentAndReferenceRequest]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/service/BusinessRuleService.kt
  - src/main/resources/data/OptionalDocument.json
  - src/main/resources/data/OptionalReference.json
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - runtime/business-rules-flow.md
  - integrations/decisioning-rules.md
---
# Business rules response model

- `BusinessRuleService` reads static optional-document, optional-reference, and party-role lists from classpath JSON. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/BusinessRuleService.kt:21)
- The mandatory-party request carries service, direction, ports, countries, cargo type, transport mode, container, and `customerCode`. (source: src/main/kotlin/com/maersk/iom/webintegrator/model/dto/MandatoryPartyRequest.kt:5)
- The mandatory-document/reference request adds `validationService`, typically `ALL`, plus the same shipment attributes. (source: src/main/kotlin/com/maersk/iom/webintegrator/model/dto/MandatoryDocumentAndReferenceRequest.kt:5)
- `buildBusinessRuleResponse(...)` subtracts mandatory items from the optional sets and suppresses `Customer Reference Number` when the customer is Project44-enabled. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/BusinessRuleService.kt:86)
