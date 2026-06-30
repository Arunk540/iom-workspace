---
category: domain
title: Vessel reference data
summary: Vessel owns searchable vessel master data plus create/update and soft-delete operations over a single reactive table.
primary_for: [vessel-reference-api]
mentions: [vessels, soft-delete, validation, active-status]
scenarios:
  - find vessel endpoint
  - trace deactivate flow
  - see vessel table
  - inspect vessel search
  - find vessel validation
capabilities: [domain-summary]
domains: [vessel]
entities: [VesselController, VesselServiceImpl, VesselEntity]
sources:
  - vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/VesselController.kt
  - vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/service/VesselServiceImpl.kt
  - vessel/vessel-persistence/src/main/kotlin/com/maersk/iom/master/data/vessel/persistence/entity/VesselEntity.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - runtime/vessel-management-flow.md
  - contracts/db-schemas.md
  - navigation/scenarios.md
---

# Scope

- `GET /vessels` searches by text and active status.
- `POST /vessels` performs create-or-update semantics on type + vesselCode.
- `DELETE /vessels/{vesselCode}` is a soft delete that flips `active_status` false.
