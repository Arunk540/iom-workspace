---
category: runtime
title: User preferences flow
summary: Preference CRUD extracts user identity from JWT, validates the allowed product set, and forwards requests to the internal agent-portal user-management API.
primary_for: [user-preferences-flow]
mentions: [user preferences, saved filters, preference crud, user settings]
scenarios:
  - user preferences flow
  - user preferences debug
  - user settings flow
  - create preferences
  - delete preferences
capabilities: [user-preferences]
domains: [Preferences]
entities: [UserPreferencesResponse, PreferenceSchema]
sources:
  - src/main/kotlin/com/maersk/iom/webintegrator/controller/UserPreferencesController.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/utils/JwtTokenUtils.kt
  - src/main/kotlin/com/maersk/iom/webintegrator/webclient/UserPreferencesClient.kt
verified_against: 5330b402a2e322f42339c190abba348c2ce2fa9d
last_updated: 2026-06-30
related:
  - integrations/user-preferences.md
  - domain/user-preferences.md
---
# User preferences flow

- The controller extracts the user email from `preferred_username` in the bearer token before every read/update/delete operation. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/UserPreferencesController.kt:47)
- Requests are allowed only for `INLAND`, `INLAND_TASKS`, and `INLAND_AUTO_BOOKING`; other product names fail fast with `IllegalArgumentException`. (source: src/main/kotlin/com/maersk/iom/webintegrator/controller/UserPreferencesController.kt:54)
- Downstream reads call `/user/{email}/product/{productName}/preferences`, while updates and deletes use `/user/{email}/product/{productName}/preference/{preferenceId}`. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/UserPreferencesClient.kt:33)
- Create calls post to `/user/preference`, and all four operations use the shared retry policy. (source: src/main/kotlin/com/maersk/iom/webintegrator/webclient/UserPreferencesClient.kt:50)
