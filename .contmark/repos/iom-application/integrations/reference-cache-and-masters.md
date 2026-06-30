---
category: integrations
title: Reference cache and mastered lookups
summary: Documents the cached reference-data paths for containers, commodities, charges, VAT partners, reasons, VAS, and ocean carriers.
primary_for: [reference-master-cache]
mentions: [reference cache, commodity master, container master, charge master]
scenarios:
  - how cache warms
  - commodity cache lookup
  - master data refresh
  - reference cache owners
  - startup cache logic
capabilities: [integration-mapping]
domains: [reference-data]
entities: [ReferenceDataFetcher, CommodityResolver, CommodityClient]
sources:
  - reference-cache/src/main/kotlin/com/maersk/iom/config/ReferenceDataFetcher.kt
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/config/WebClientConfiguration.kt
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/commodity/CommodityClient.kt
  - resolvers/src/main/kotlin/com/maersk/iom/resolvers/CommodityResolver.kt
  - reference-data-client/src/main/kotlin/com/maersk/iom/webclient/vas/VasClient.kt
verified_against: 7a0a2b25221d850945899c2497f890b88f6143df
last_updated: 2026-06-30
related:
  - integrations/customer-and-facility.md
  - operations/failure-model.md
---
## Cache wiring

- `WebClientConfiguration` registers dedicated WebClient beans for charges, commodity, container, VAT partner, VAS, ocean carrier, amend reasons, and cancel reasons, each keyed from `services.*.base-url` properties and common default headers. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/config/WebClientConfiguration.kt:77)
- `ReferenceDataFetcher` starts only when cache flags are enabled, then warms container, commodity, charge, VAT-partner, amend-reason, cancel-reason, VAS, and ocean-carrier caches into `ConcurrentHashMap` instances. (source: reference-cache/src/main/kotlin/com/maersk/iom/config/ReferenceDataFetcher.kt:55) (source: reference-cache/src/main/kotlin/com/maersk/iom/config/ReferenceDataFetcher.kt:82)
- The same fetcher refreshes these caches on cron schedules for container, commodity, charge, VAT-partner, amend/cancel reason, VAS, and ocean-carrier data. (source: reference-cache/src/main/kotlin/com/maersk/iom/config/ReferenceDataFetcher.kt:302)

## Call patterns

- `CommodityClientImpl.get()` queries the commodity master by `commodityCode`, sends `API-Version`, filters the response list to the requested code, and retries through the shared retry policy. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/commodity/CommodityClient.kt:19)
- `VasClientImpl.getVases()` performs a simple GET against the VAS master endpoint and maps directly into `VasMasterDataResponseModel`. (source: reference-data-client/src/main/kotlin/com/maersk/iom/webclient/vas/VasClient.kt:15)
- `CommodityResolver` prefers cached commodity values and only falls back to `CommodityClient` when the cache miss occurs or cache mode is disabled. (source: resolvers/src/main/kotlin/com/maersk/iom/resolvers/CommodityResolver.kt:53)
