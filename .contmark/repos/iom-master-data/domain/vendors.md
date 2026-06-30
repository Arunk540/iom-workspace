---
category: domain
title: Vendor master data
summary: Vendors runs parallel V1 and V2 vendor read APIs plus Kafka-driven ingestion pipelines that persist both source payloads and normalized rows.
primary_for: [vendor-master-data]
mentions: [vendors, kafka, v1, v2, avro]
scenarios:
  - trace vendor kafka
  - find vendor v2 search
  - see vendor tables
  - inspect source tracking
  - find consumer groups
capabilities: [domain-summary]
domains: [vendors]
entities: [VendorController, VendorV2Controller, VendorDataConsumer, VendorV2DataConsumer]
sources:
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/controller/VendorController.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/controller/VendorV2Controller.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorDataConsumer.kt
  - vendors/vendor-controller/src/main/kotlin/com/maersk/iom/master/data/vendor/service/VendorV2DataConsumer.kt
  - vendors/vendor-persistence/src/main/kotlin/com/maersk/iom/master/data/vendor/persistence/entity/VendorDataEntity.kt
  - vendors/vendor-persistence/src/main/kotlin/com/maersk/iom/master/data/vendor/persistence/entity/VendorDataV2Entity.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - runtime/vendor-ingest-flow.md
  - contracts/kafka-events.md
  - integrations/vendor-feeds.md
---

# Scope

- REST read/search is versioned as `/vendors` and `/v2/vendors`.
- Kafka is the primary ingest path for both versions, even though direct POST upserts also exist in the controllers.
- Each version persists both normalized vendor rows and a source/audit table with the original payload.

## Data model

- V1: `vendors` plus `vendors_source`.
- V2: `vendors_v2` plus `vendors_source_v2`.
- V2 additionally stores JSON addresses and purchasing-org arrays plus VMDM/MDG/SMDS code relationships.
