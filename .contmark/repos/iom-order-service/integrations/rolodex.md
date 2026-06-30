---category: integrations
title: Rolodex storage integration
summary: Rolodex is the explicit external file/auth integration for report templates and downloads. The service first fetches a bearer token, then downloads template/content bytes over WebClient.
primary_for: [rolodex-integration]
mentions: [rolodex-auth, template-download, excel-template, webclient]
scenarios:
  - inspect rolodex auth
  - inspect template download
  - inspect external storage
  - inspect report template ids
  - inspect excel bytes path
  - find the rolodex call
capabilities: [integrations]
domains: [reporting]
entities: [RolodexTokenResponse, WebClient, ByteArray]
sources:
  - src/main/resources/application.yaml
  - src/main/kotlin/com/maersk/iom/service/RolodexAuthService.kt
  - src/main/kotlin/com/maersk/iom/service/ExcelService.kt
  - src/main/kotlin/com/maersk/iom/service/ExportExcelDataService.kt
verified_against: 77238b3248beddfc1396ff2839a940f29e281719
last_updated: 2026-06-30
related:
  - runtime/dashboard-query-flow.md
  - operations/failure-model.md
  - stack/stack.md
---
- YAML provides `rolodex.client-id`, `scope`, `client-secret`, `grant-type`, `auth-url`, `tenant-id`, `rolodex-url`, `rolodex-download-url`, and template `folder-id`/`file-id`. (source: src/main/resources/application.yaml:456-468)
- `RolodexAuthService.getAuthToken()` posts form-urlencoded client-credentials data and returns `Bearer <access_token>`; missing config fails fast. (source: src/main/kotlin/com/maersk/iom/service/RolodexAuthService.kt:17-49)
- `ExcelService.downloadFromExternalStorage(...)` builds `/folders/{folderId}/files/{fileId}`, adds tenant and authorization headers, and downloads raw bytes with WebClient. (source: src/main/kotlin/com/maersk/iom/service/ExcelService.kt:15-38)
- `ExportExcelDataService` reads Rolodex template folder/file ids from configuration and combines them with local workbook generation/export logic. (source: src/main/kotlin/com/maersk/iom/service/ExportExcelDataService.kt:29-36)
