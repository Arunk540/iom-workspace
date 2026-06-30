---
category: domain
title: User preference model
summary: Preference APIs are keyed by the email extracted from the bearer token and grouped by product name such as `INLAND`, `INLAND_TASKS`, and `INLAND_AUTO_BOOKING`.
primary_for: [user-preference-model]
mentions: [preferences, user settings, filters, product]
scenarios:
  - user preferences model
  - preference product rules
  - preference email lookup
  - create preference payload
  - delete preference payload
capabilities: [domain-model]
domains: [Preferences]
entities: [UserPreferencesResponse, PreferenceSchema, CreatePreferenceRequestSchema]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/controller/UserPreferencesController.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/utils/JwtTokenUtils.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - runtime/user-preferences-flow.md
  - integrations/user-preferences.md
---
# User preference model

- The controller defaults `productName` to `INLAND` and only accepts `INLAND`, `INLAND_TASKS`, and `INLAND_AUTO_BOOKING`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/UserPreferencesController.kt:31)
- Email identity is derived from the JWT `preferred_username` claim. (source: src/main/kotlin/com/maersk/iom/webintegrator/utils/JwtTokenUtils.kt:8)
- Downstream CRUD returns generated OpenAPI models such as `UserPreferencesResponse` and `PreferenceSchema`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/UserPreferencesController.kt:47)
