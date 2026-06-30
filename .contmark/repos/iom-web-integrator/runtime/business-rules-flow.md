---
category: runtime
title: Business rules flow
summary: Business-rule evaluation fans out to mandatory-party rules, mandatory document/reference rules, and SCM onboarding rules before building a consolidated response.
primary_for: [business-rules-flow]
mentions: [business rules, mandatory docs, mandatory parties, project44]
scenarios:
  - business rules flow
  - mandatory parties flow
  - mandatory docs flow
  - project44 flow
  - rules debug path
capabilities: [business-rules]
domains: [Compliance]
entities: [BusinessRuleRequest, BusinessRuleResponse]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/controller/BusinessRuleController.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/service/BusinessRuleService.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/decisioningservice/MandatoryPartyClient.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/decisioningservice/MandatoryDocumentAndReferenceClient.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/decisioningservice/ScmCustomerClient.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - integrations/decisioning-rules.md
  - domain/business-rules.md
---
# Business rules flow

- `BusinessRuleController.getBusinessRules(...)` accepts a reactive `BusinessRuleRequest` and delegates immediately to `BusinessRuleService.getBusinessRules(...)`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/BusinessRuleController.kt:26)
- The service builds two downstream DTOs: `MandatoryPartyRequest` and `MandatoryDocumentAndReferenceRequest`, both derived from the same shipment attributes. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/BusinessRuleService.kt:49)
- Mandatory-party rules are fetched via `MandatoryPartyClient.getMandatoryParty(...)`; downstream HTTP errors are folded into a response `Status` instead of failing the whole request. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/decisioningservice/MandatoryPartyClient.kt:28)
- Mandatory-document/reference rules follow the same pattern and also degrade to a `Status` payload on extended external API errors. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/decisioningservice/MandatoryDocumentAndReferenceClient.kt:32)
- If `customerCode` is present, `ScmCustomerClient.checkCustomerOnboardingRules(...)` decides whether the response should suppress Project44-specific optional references. (source: src/main/kotlin/com/maersk/iom/webintegrator/service/BusinessRuleService.kt:76)
