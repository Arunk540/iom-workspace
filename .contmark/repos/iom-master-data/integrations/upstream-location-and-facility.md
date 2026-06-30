---
category: integrations
title: Upstream location and facility services
summary: Location queries depend on two WebClient integrations: a Maersk geography location API and a facility search API secured with Azure AD client credentials.
primary_for: [location-upstream-integrations]
mentions: [locationClient, facilityClient, webclient, azuread, akamai]
scenarios:
  - find location base url
  - trace facility oauth
  - inspect external headers
  - see location integration
  - debug facility retry
capabilities: [integration-map]
domains: [location]
entities: [WebClientConfiguration, LocationClientImpl, FacilityClientImpl]
sources:
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/configuration/WebClientConfiguration.kt
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/location/LocationClient.kt
  - location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/facility/FacilityClient.kt
  - service/src/main/resources/application.yaml
verified_against: 31e9ef5232464b4ada31300fa65bca7b209345c9
last_updated: 2026-06-30
related:
  - runtime/location-search-flow.md
  - operations/retries.md
  - domain/location.md
---

# Systems

- `locationClient` targets `${services.location.base-url}` and adds both `X-Requestor` and the location consumer key header. (source: location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/configuration/WebClientConfiguration.kt:109; service/src/main/resources/application.yaml:287)
- `facilityClient` targets `${services.facility.base-url}`, applies Azure AD client-credentials OAuth via `facilityAuthProvider`, and also sets the consumer key header. (source: location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/configuration/WebClientConfiguration.kt:119; service/src/main/resources/application.yaml:290)
- Both clients share configured connect/read/write timeouts and log request/response metadata through WebClient filters. (source: location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/configuration/WebClientConfiguration.kt:44)
- Search requests call upstream location endpoints with `pageSize=250`, while facility requests convert `CUSTOMER` into `facilityType=CUST` and everything else into `OPS`. (source: location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/location/LocationClient.kt:45; location/location-controller/src/main/kotlin/com/maersk/iom/master/data/location/webclient/facility/FacilityClient.kt:31)
