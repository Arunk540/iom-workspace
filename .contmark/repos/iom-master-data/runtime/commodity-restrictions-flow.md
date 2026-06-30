---
category: runtime
title: Commodity restriction filtering flow
summary: How POST /commodities intersects cached commodity data with a remote restriction response.
primary_for: [commodity-restriction-flow]
mentions: [commodity, hs-code, restriction, business-rules, startup-load]
scenarios:
  - trace restricted commodity
  - debug commodity post
  - inspect business rules call
  - find commodity filter
  - follow commodity cache
capabilities: [runtime-flow]
domains: [commodity]
entities: [CommodityController, CommodityServiceImpl, CommodityLoadService, CommodityRestrictionClientImpl]
sources:
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/CommodityController.kt
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/service/CommodityServiceImpl.kt
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/webclient/businessrule/CommodityRestrictionClient.kt
  - commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/webclient/configuration/ErrorHandler.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - integrations/business-rules-service.md
  - operations/retries.md
  - domain/commodity.md
---

# Flow

1. `POST /commodities` is handled by `CommodityController.getNonRestrictedCommodities`, which builds the same search predicate set used by the GET endpoint. (source: commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/CommodityController.kt:101)
2. `CommodityServiceImpl` first resolves the candidate commodity set from `CommodityLoadService`, meaning the initial search is fulfilled from startup-loaded data rather than a fresh repository query. (source: commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/service/CommodityServiceImpl.kt:38)
3. The service then calls `CommodityRestrictionClient.getCommodityRestrictions` with a transformed request body and extracts the restriction code list from the remote response payload. (source: commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/service/CommodityServiceImpl.kt:42)
4. The final response zips both streams and removes any commodity whose `commodityCode` appears in the returned restriction list. (source: commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/service/CommodityServiceImpl.kt:47)
5. `CommodityRestrictionClientImpl` posts JSON to the configured restricted-commodity URL and retries 5xx-backed `ExternalApiException` failures with the shared commodity retry configuration. (source: commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/webclient/businessrule/CommodityRestrictionClient.kt:30; commodity/commodity-controller/src/main/kotlin/com/maersk/iom/master/data/commodity/controller/webclient/configuration/ErrorHandler.kt:38)
