---
category: integrations
title: Business Rules restricted commodity service
summary: Commodity POST requests call an OAuth-protected Business Rules endpoint to fetch restricted commodity codes.
primary_for: [commodity-business-rules-integration]
mentions: [business-rules, restricted-commodity, oauth2, webclient, forgerock]
scenarios:
  - find restriction url
  - trace commodity oauth
  - inspect business rules client
  - debug restricted commodity call
  - see business rules headers
capabilities: [integration-map]
domains: [commodity]
entities: [CommodityWebClientConfiguration, CommodityRestrictionClientImpl]
sources:
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/webclient/configuration/CommodityWebClientConfiguration.kt
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/webclient/businessrule/CommodityRestrictionClient.kt
  - service/src/main/resources/application.yaml
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - runtime/commodity-restrictions-flow.md
  - operations/retries.md
  - domain/commodity.md
---

# System

- `commodityRestrictionClient` points at `${services.restricted-commodity.url}`, which is derived from `${services.business-rules.base-url}`. (source: commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/webclient/configuration/CommodityWebClientConfiguration.kt:122; service/src/main/resources/application.yaml:293)
- OAuth tokens come from the `iomAuthProvider` client-credentials registration and use a dedicated WebClient token-response client that adds `X-Requestor` and `ciamConsumerKey`. (source: commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/webclient/configuration/CommodityWebClientConfiguration.kt:91; service/src/main/resources/application.yaml:57)
- The outbound call is a JSON `POST` and maps the body directly into `CommodityRestrictionResponse`. (source: commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/webclient/businessrule/CommodityRestrictionClient.kt:30)
