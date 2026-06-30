---category: integrations
title: Identity providers
summary: The service relies on external Azure AD and ForgeRock issuer/JWKS endpoints for JWT validation rather than custom outbound business REST calls.
primary_for: [facilities-jwt-providers]
mentions: [azure, forgerock, oauth2, issuer-uri, jwk-set-uri, authorization]
scenarios:
  - inspect jwt integration
  - inspect auth providers
  - inspect issuer endpoints
  - inspect jwks endpoints
  - inspect bearer auth config
  - find identity providers
capabilities: [integration-analysis]
domains: [smds-facilities]
entities: [AzureAD, ForgeRock, Authorization]
sources:
  - service/src/main/resources/application.yml
  - service/src/main/resources/application-local.yml
  - service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/controller/SmdsFacilitiesController.java
  - componenttest/src/test/java/TestRunner.java
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - contracts/api-contracts.md
  - operations/failure-model.md
---

# Identity providers

- The default profile configures Spring Security resource-server support for two external JWT providers: Azure (`issuer-uri`/`jwk-set-uri`) and ForgeRock (`issuer-uri`/`jwk-set-uri` plus a consumer key). (source: service/src/main/resources/application.yml:53-63)
- The local profile carries the same dual-provider shape, with Azure endpoints derived from `TENANT_ID` and ForgeRock endpoints pointing at Maersk CIAM validation services. (source: service/src/main/resources/application-local.yml:29-35)
- Every public endpoint is protected by a bearer `Authorization` header plus `@PreAuthorize` role checks. (source: service/src/main/java/net/apmoller/crb/telikos/microservices/smds/facilities/application/controller/SmdsFacilitiesController.java:35-37,140-142,250-252,345-347)
- Component tests inject the Azure and ForgeRock issuer/JWKS environment variables when booting the service container, confirming those endpoints are required at runtime. (source: componenttest/src/test/java/TestRunner.java:69-80)

## Related

- [[contracts/api-contracts]]
- [[operations/failure-model]]
