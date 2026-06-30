---category: operations
title: Monitoring and diagnostics
summary: Observability is built around Actuator health/prometheus endpoints, HTTP latency percentiles, and package-level logging.
primary_for: [facilities-monitoring]
mentions: [actuator, prometheus, health, metrics, logging]
scenarios:
  - inspect monitoring setup
  - inspect prometheus endpoint
  - inspect health probes
  - inspect request metrics
  - inspect log levels
  - find monitoring setup
capabilities: [operational-analysis]
domains: [smds-facilities]
entities: [management.endpoints, prometheus, logging.level]
sources:
  - service/src/main/resources/application.yml
  - service/src/main/resources/application-local.yml
verified_against: 59b572b95e052b35a641bea35f72c4494a6d37e2
last_updated: 2026-06-30
related:
  - architecture/cross-cutting.md
  - operations/failure-model.md
---

# Monitoring and diagnostics

- Actuator exposes `health`, `info`, and `prometheus` over the web endpoint configuration. (source: service/src/main/resources/application.yml:86-92)
- HTTP server metrics publish 90th/95th/99th percentiles, enable histograms, and set a six-second maximum expected value for request timing. (source: service/src/main/resources/application.yml:79-85)
- Health details are always shown, metrics are enabled for health, and liveness probes include `diskSpace` and `ping`. (source: service/src/main/resources/application.yml:93-104)
- Default logging is package-tuned with application and infrastructure at `INFO`, domain at `DEBUG`, and PostgreSQL query/parameter logging enabled. (source: service/src/main/resources/application.yml:30-36)
- The local profile raises verbosity further, with application `DEBUG`, infrastructure/domain `TRACE`, and PostgreSQL query/parameter logging still enabled. (source: service/src/main/resources/application-local.yml:112-118)

## Related

- [[architecture/cross-cutting]]
- [[operations/failure-model]]
