---
category: runtime
title: Vessel CRUD and soft-delete flow
summary: How vessel search, upsert, and deactivation work against the reactive vessel repository.
primary_for: [vessel-crud-flow]
mentions: [vessels, search, upsert, soft-delete, active-status]
scenarios:
  - trace vessel api
  - inspect vessel upsert
  - see deactivation rule
  - find vessel search
  - follow active flag logic
capabilities: [runtime-flow]
domains: [vessel]
entities: [VesselController, VesselServiceImpl, VesselRepository]
sources:
  - vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/VesselController.kt
  - vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/service/VesselServiceImpl.kt
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - domain/vessel.md
  - contracts/db-schemas.md
  - navigation/scenarios.md
---

# Flow

1. `GET /vessels` requires non-blank `searchText`, optionally filters by `activeStatus`, and delegates to `VesselServiceImpl.searchVessels`. (source: vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/VesselController.kt:39; vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/service/VesselServiceImpl.kt:18)
2. Search queries either call `searchByText` or `searchByTextAndActiveStatus` and emit `NotFoundException` when no rows match. (source: vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/service/VesselServiceImpl.kt:18)
3. `POST /vessels` rejects an empty list in the controller, then upserts each request by checking `type + vesselCode` before deciding update versus insert. (source: vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/VesselController.kt:71; vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/service/VesselServiceImpl.kt:30)
4. `DELETE /vessels/{vesselCode}` performs a soft delete by finding the row, rejecting already-inactive rows, and calling `deactivateByVesselCode`. (source: vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/VesselController.kt:106; vessel/vessel-controller/src/main/kotlin/com/maersk/iom/master/data/vessel/controller/service/VesselServiceImpl.kt:49)
