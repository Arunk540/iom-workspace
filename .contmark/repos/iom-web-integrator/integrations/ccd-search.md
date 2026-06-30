---
category: integrations
title: CCD search integration
summary: CCD contract lookups are delegated to a dedicated downstream service with its own OAuth2 client registration and extended external error mapping.
primary_for: [ccd-search-integration]
mentions: [ccd, contracts, agreement lookup, customer scope]
scenarios:
  - ccd search integration
  - ccd search backend
  - contract search backend
  - agreement integration
  - customer scope search
capabilities: [integration-ccd-search]
domains: [Contracts]
entities: [CcdAgreementResponse, CcdSearchResponse]
sources:
  - src/main/resources/application.yml
  - src/main/kotlin/com/maersk/iom/webintegrator/config/WebClientConfiguration.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/CcdSearchClient.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/mapper/CcdSearchResponseMapper.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - runtime/business-rules-flow.md
  - contracts/api-contracts-1.md
---
# CCD search integration

- Base URL is `services.ccd-search.base-url`, defaulting to `https://api-cdt.maersk.com/ccd-search`, with a dedicated consumer key. (source: src/main/resources/application.yml:101)
- The WebClient uses OAuth2 client registration `ccdAuthProvider`. (source: src/main/kotlin/com/maersk/iom/webintegrator/config/WebClientConfiguration.kt:214)
- `CcdSearchClient` reads `/contracts/customer/{customerCode}/scope/{scope}` and maps any HTTP error via `handleExtendedExternalApiError(...)`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/CcdSearchClient.kt:27)
- The controller response is a reduced `CcdSearchResponse`, created from the raw agreement payload by `toCcdSearchResponse()`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/CcdSearchController.kt:31)
