# Workspace Lessons

<!-- Add lessons in the format: date | category | lesson | trigger/context -->
<!-- Consumed by contmark.skill-evolution-loop for skill patch proposals -->

| Date | Category | Lesson | Trigger |
|------|----------|--------|---------|
| 2026-06-30 | bootstrap | IOM-workspace has 7 repos: 4 runtime services (iom-offer-service, iom-order-service, iom-order-service-scheduler, iom-web-integrator), 2 domain libraries (iom-application, iom-master-data), 1 data service (smds-facilities-service) | Initial workspace bootstrap |
| 2026-06-30 | architecture | iom-application is a multi-module Gradle shared library, not a deployable service — no REST controllers at root level | bootstrap probe |
| 2026-06-30 | architecture | iom-master-data is a multi-module Gradle project split by domain (charges, commodity, container, location, vessel, etc.) | bootstrap probe |
| 2026-06-30 | architecture | smds-facilities-service uses service/ submodule structure (main source under service/, tests under componenttest/) | bootstrap probe |
| 2026-06-30 | bootstrap | Quota-interrupted bootstrap left 6 of 7 _index.json roll-ups degraded (4 dropped primary_for/scenarios; iom-order-service + iom-application wrote files[] as bare path strings). The .md frontmatter was intact, so _index.json was rebuilt deterministically from frontmatter — no re-probe needed | Step 4 resume; only iom-offer-service had a correct roll-up |
| 2026-06-30 | integrations | Cross-repo graph (_global_links): web-integrator->offer+order (REST); offer->master-data+smds (REST); order->scheduler (outbox_messages DB); scheduler->offer+order (kafka domain-event), ->offer (REST cleanup), ->web-integrator (REST reprice). order-service master-data calls go through shared resolver libs with no traced host — omitted to avoid an unverified edge | _global_links derivation |
| 2026-06-30 | quality | validate-bootstrap flags 70 frontmatter issues across all repos (62 primary_for<->scenario bridge, 4 scenario>6 words, 2 not-lowercase, 2 CamelCase) — discoverability sharpness, not correctness; resolver routing verified working regardless. Address via evolution-loop | post-setup validation |
