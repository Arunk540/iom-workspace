---
category: integrations
title: User preferences integration
summary: Preferences are stored behind the internal agent portal user-management API and accessed through product-scoped user endpoints.
primary_for: [user-preferences-integration]
mentions: [preferences backend, agent portal, saved filters, user settings api]
scenarios:
  - preferences integration
  - agent portal backend
  - saved filters backend
  - user settings backend
  - preference api dependency
capabilities: [integration-user-preferences]
domains: [Preferences]
entities: [UserPreferencesResponse, PreferenceSchema]
sources:
  - src/main/resources/application.yml
  - src/main/kotlin/com/maersk/iom/webintegrator/config/WebClientConfiguration.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/UserPreferencesClient.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - runtime/user-preferences-flow.md
  - operations/retries.md
---
# User preferences integration

- Base URL is `services.userPreferences.url = ${services.userPreferences.base-url}/v1/user-management`, defaulting to `https://api-cdt.maersk.com/internal-agent-portal/v1/user-management`. (source: src/main/resources/application.yml:97)
- The bean uses its own OAuth2 client-registration (`userPreferencesAuthProvider`) and consumer-key header. (source: src/main/kotlin/com/maersk/iom/webintegrator/config/WebClientConfiguration.kt:203)
- Reads call `/user/{email}/product/{productName}/preferences`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/UserPreferencesClient.kt:33)
- Creates call `/user/preference`; updates and deletes call `/user/{email}/product/{productName}/preference/{preferenceId}`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/UserPreferencesClient.kt:50)
- All four operations apply centralized retries and leave error mapping to default WebClient exceptions. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/UserPreferencesClient.kt:41)
