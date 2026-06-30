---category: integrations
title: Identity provider integrations
summary: JWT validation integrates with ForgeRock and Azure issuer/JWK sets, while some outbound calls use customer-identity OAuth token endpoints. This repo contains the security wiring rather than business-specific identity logic.
primary_for: [identity-provider-integration]
mentions: [forgerock, azure, jwt, jwk, oauth2]
scenarios:
  - inspect jwt issuer
  - inspect jwk lookup
  - inspect oauth client
  - inspect role extraction
  - inspect auth bypass
  - find identity providers
  - check identity config
capabilities: [integrations]
domains: [security]
entities: [ReactiveJwtDecoder, Jwt, NimbusReactiveJwtDecoder, WebClient]
sources:
  - src/main/resources/application.yaml
  - src/main/kotlin/com/maersk/iom/config/SecurityConfig.kt
  - src/main/kotlin/com/maersk/iom/config/DefaultProfile.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - architecture/cross-cutting.md
  - operations/monitoring.md
  - operations/failure-model.md
---
- YAML binds Azure and ForgeRock issuer/JWK URIs plus customer-identity token URIs for `iomAuthProvider` and `telikosAuthProvider`. (source: src/main/resources/application.yaml:91-96, src/main/resources/application.yaml:188-218)
- `SecurityConfig` authenticates every non-actuator/non-Swagger request and converts JWT roles or scopes into Spring authorities. (source: src/main/kotlin/com/maersk/iom/config/SecurityConfig.kt:79-99, src/main/kotlin/com/maersk/iom/config/SecurityConfig.kt:111-150)
- `reactiveJwtDecoder` peeks at `iss` and chooses ForgeRock vs Azure decoder beans, or a secret-key decoder when configured. (source: src/main/kotlin/com/maersk/iom/config/SecurityConfig.kt:159-209)
- Decoder beans create JWK-backed `NimbusReactiveJwtDecoder` instances and attach issuer validation; requests to JWK endpoints include IOM user-agent and consumer key headers. (source: src/main/kotlin/com/maersk/iom/config/SecurityConfig.kt:212-243)
- `DefaultProfile` can disable the reactive resource-server setup when JWT validators are turned off. (source: src/main/kotlin/com/maersk/iom/config/DefaultProfile.kt:10-13)
