---
category: integrations
title: Azure Blob Storage
summary: Archival code writes gzip-compressed JSON blobs to Azure Blob Storage, then records blob metadata and deletes source rows. All blob access goes through `BlobUploader`.
primary_for: [azure-blob-archival]
mentions: [order-archival, audit-archival, gzip-blob-upload]
scenarios:
  - archive upload failing
  - blob path issue
  - azure blob archive
  - where blobs uploaded
  - gzip upload flow
capabilities: [archival]
domains: [IOM]
entities: [ArchivalMetaEntity, ServicePlanEntity, AuditEntity]
peer_systems: [azure-blob-storage]
direction: outbound
protocol: rest
topic_or_endpoint: azure.storage.blob.container-name
sources:
  - src/main/kotlin/com/maersk/iom/config/AzureBlobStorageConfiguration.kt
  - src/main/kotlin/com/maersk/iom/archive/service/BlobUploader.kt
  - src/main/kotlin/com/maersk/iom/archive/service/OrderArchivalService.kt
  - src/main/kotlin/com/maersk/iom/archive/service/AuditArchivalService.kt
  - src/main/resources/application.yaml
verified_against: 80df265cec4c4868b08e3ca78fb9657d5925cada
last_updated: 2026-06-30
related:
  - operations/failure-model.md
  - navigation/key-classes.md
---

# Azure Blob Storage integration

- `AzureBlobStorageConfiguration` builds `BlobServiceClient` from `azure.storage.blob.connection-string` and resolves a `BlobContainerClient` using `azure.storage.blob.container-name` (source: src/main/kotlin/com/maersk/iom/config/AzureBlobStorageConfiguration.kt:18; source: src/main/resources/application.yaml:44)
- `BlobUploader.uploadServicePlanEntityToBlobStorage(...)` serializes `SerializedEntity` to JSON, gzips the bytes, and uploads them through `blobContainerClient.getBlobClient(fileName)` (source: src/main/kotlin/com/maersk/iom/archive/service/BlobUploader.kt:17)
- Order archival writes blob paths as `ServicePlans/yyyy/MM/dd/{servicePlanNumber}.json.gz` using `lastModifiedTime` (source: src/main/kotlin/com/maersk/iom/archive/service/OrderArchivalService.kt:49)
- Audit archival writes blob paths as `Audits/yyyy/MM/dd/{servicePlanNumber}_{id}.json.gz` using `createdTime` (source: src/main/kotlin/com/maersk/iom/archive/service/AuditArchivalService.kt:53)
- Upload returns `ArchivalMetaEntity(key, entityClass, url)` for the follow-on metadata save step, and `IllegalStateException` from Azure upload is logged then ignored in-place (source: src/main/kotlin/com/maersk/iom/archive/service/BlobUploader.kt:25)
