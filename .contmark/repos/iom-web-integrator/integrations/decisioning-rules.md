---
category: integrations
title: Decisioning and rules integrations
summary: Business-rule evaluation depends on three business-rules endpoints: mandatory parties, mandatory documents/references, and SCM customer onboarding.
primary_for: [decisioning-rules-integration]
mentions: [decisioning, mandatory rules, scm onboarding, business rules]
scenarios:
  - decisioning integration
  - mandatory parties backend
  - mandatory documents backend
  - scm onboarding backend
  - business rules dependency
capabilities: [integration-decisioning]
domains: [Compliance]
entities: [MandatoryPartyRequest, MandatoryDocumentAndReferenceRequest, CustomerScmResponse]
sources:
  - src/main/resources/application.yml
  - src/main/kotlin/com/maersk/iom/webintegrator/config/WebClientConfiguration.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/decisioningservice/MandatoryPartyClient.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/decisioningservice/MandatoryDocumentAndReferenceClient.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/decisioningservice/ScmCustomerClient.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - runtime/business-rules-flow.md
  - domain/business-rules.md
---
# Decisioning and rules integrations

- All three clients share the business-rules base URL `https://api-cdt.maersk.com/dh` by default and use the same business-rules consumer key. (source: src/main/resources/application.yml:48)
- `mandatory-party` resolves to `/mandatory-party/mandatory-party-rules`. (source: src/main/resources/application.yml:51)
- `mandatory-document-reference` resolves to `/mandatory-documents/mandatory-documents-rules`. (source: src/main/resources/application.yml:53)
- `scm-customer` resolves to `/telikos-customer-scm-onboarding/customer-scm-rules`. (source: src/main/resources/application.yml:55)
- Each WebClient bean attaches OAuth2 client credentials and consumer key headers before requests are made. (source: src/main/kotlin/com/maersk/iom/webintegrator/config/WebClientConfiguration.kt:65)
- Mandatory-party and mandatory-document clients downgrade downstream failures into `Status`-bearing payloads, while SCM onboarding propagates extended external API errors. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/decisioningservice/MandatoryPartyClient.kt:38)
