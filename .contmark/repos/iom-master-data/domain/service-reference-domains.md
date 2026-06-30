---category: domain
title: Service-hosted reference domains
summary: The service module still hosts smaller reference-data domains such as country, currency, reasons, VAS, layout types, carriers, customs, and IMO classifications.
primary_for: [service-hosted-reference-data]
mentions: [service, country, currency, reasons, vas, layout-types]
scenarios:
  - find service controller
  - trace reasons data
  - inspect vas source
  - see thin controllers
  - find host module domains
  - find reference data
  - look up reference domains
capabilities: [domain-summary]
domains: [service-misc]
entities: [CountryController, ReasonController, VasController, VasEntity]
sources:
  - service/src/main/kotlin/com/maersk/iom/master/data/country/controller/CountryController.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/reasons/controller/ReasonController.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/vas/controller/VasController.kt
  - service/src/main/kotlin/com/maersk/iom/master/data/vas/entity/VasEntity.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - navigation/entry-points-1.md
  - contracts/db-schemas.md
  - stack/stack.md
---

# Hosted domains

- Thin controller-only domains: country, customs products, currencies, emergency temperatures, FinOps carriers, ocean carriers, IMO classifications, layout types.
- Reasons are loaded from classpath JSON files in `ReasonController`.
- VAS is the exception with `VasService` and the `value_added_services` table, while `/vas` remains a deprecated alias backed by local JSON.
- New major domains should be promoted to top-level Gradle modules instead of being added here.
