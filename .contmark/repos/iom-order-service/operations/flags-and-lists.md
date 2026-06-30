---
category: operations
title: Flags and lists
summary: Runtime behavior is shaped by YAML feature toggles, LaunchDarkly gates, scheduler lists, VAT country lists, and configured system-channel allow lists. Several flows also depend on embedded constant lists such as repricing windows and demo execution recipients.
primary_for: [flags-and-lists]
mentions: [launchdarkly, features, scheduler-lists, vat-countries, system-channels]
scenarios:
  - inspect feature flags
  - inspect scheduler lists
  - inspect vat list
  - inspect channel allowlist
  - inspect launchdarkly keys
capabilities: [operations]
domains: [configuration]
entities: [FeatureFlag, LDReactorClient, CustomerDateDetails, CreatedBySystemChannelsProperties]
sources:
  - src/main/resources/application.yaml
  - src/main/kotlin/com/maersk/iom/config/LaunchDarklyLocalConfig.kt
  - src/main/kotlin/com/maersk/iom/scheduler/CustomerBookingHistoryScheduler.kt
  - src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt
  - src/main/kotlin/com/maersk/iom/order/domain/config/CreatedBySystemChannelsProperties.kt
  - src/main/kotlin/com/maersk/iom/validator/AmendmentJourneyValidator.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - operations/retries.md
  - operations/monitoring.md
  - integrations/master-data-and-rules.md
---
- YAML feature toggles include validation switches, outbox enablement, master-data resolver toggles, document validation, SAP/TMS rules validation, vessel/appointment related flags, and booked-by optional channels `SCP,WEBEC,EDI,IP`. (source: src/main/resources/application.yaml:263-285)
- LaunchDarkly config carries `sdkKey`, `mcimSdkKey`, and `micmEnabled`; local profile reads flags from `FeatureCatalog.json`. (source: src/main/resources/application.yaml:24-28, src/main/kotlin/com/maersk/iom/config/LaunchDarklyLocalConfig.kt:20-34)
- The customer-history scheduler is keyed by LaunchDarkly flag `mci-iom-is-customer-history-scheduler-enabled` and is parameterized by `scheduler.countryCodes`, `scheduler.bookedByCodes`, `batchSize`, `batchConcurrency`, `countryConcurrency`, and `processingBatchSize`. (source: src/main/kotlin/com/maersk/iom/scheduler/CustomerBookingHistoryScheduler.kt:30-67, src/main/resources/application.yaml:31-47)
- VAT-mandated countries are configured as `KE,UG`. (source: src/main/resources/application.yaml:48-49)
- Created-by system channels are configured as `SCP`, `WEBEC`, `EDI`, and `IP`, and `CreatedBySystemChannelsProperties` normalizes case for membership checks. (source: src/main/resources/application.yaml:301-307, src/main/kotlin/com/maersk/iom/order/domain/config/CreatedBySystemChannelsProperties.kt:6-24)
- Customer-date repricing windows are explicitly listed for `VN00021887` and `VN01372490`. (source: src/main/resources/application.yaml:289-300)
- Code-visible feature names include `booking_be-iom_lazy_transform_enabled`, single-click confirm / migrated-to-execution checks, bulk amend enablement, and vessel ETD-based PCD checks. (source: src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:490-504, src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:711-720, src/main/kotlin/com/maersk/iom/order/domain/OrderServiceImpl.kt:1159-1166, src/main/kotlin/com/maersk/iom/messaging/BookingUpdatesEventHandler.kt:107-138, src/main/kotlin/com/maersk/iom/validator/AmendmentJourneyValidator.kt:15-27)
