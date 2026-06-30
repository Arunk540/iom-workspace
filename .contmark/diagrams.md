# IOM-workspace — whole-system diagram

Derived from the per-repo mini-skills + `_global_links.json` (the single source of truth → cannot
drift). Cross-repo edges are exactly the 8 entries in `_global_links.json`. External systems come from
each repo's `integrations/`. Regenerate when the underlying mini-skills change.

```mermaid
flowchart TB
  classDef repo fill:#e8f0fe,stroke:#3367d6,color:#111;
  classDef ext fill:#f1f3f4,stroke:#9aa0a6,color:#111,stroke-dasharray:3 3;

  subgraph WI[iom-web-integrator — frontend BFF]
    WIc[OfferClient / OrderClient<br/>booking + service-plan orchestration]
  end
  subgraph OFF[iom-offer-service — offer search / rates]
    OFFc[Offer search · rates · reprice<br/>offered-plan MongoDB]
  end
  subgraph ORD[iom-order-service — service-plan / order]
    ORDc[Service-plan CRUD · dashboard<br/>outbox staging]
  end
  subgraph SCH[iom-order-service-scheduler — outbox + jobs]
    SCHc[Outbox publisher · offer cleanup<br/>Temporal repricing]
  end
  subgraph MD[iom-master-data]
    MDc[location · commodity · charge-type<br/>container · sales · vessel]
  end
  subgraph SMDS[smds-facilities-service]
    SMDSc[facility data + search]
  end
  subgraph APP[iom-application]
    APPc[shared domain-model library]
  end

  class WI,OFF,ORD,SCH,MD,SMDS,APP repo;
  class WIc,OFFc,ORDc,SCHc,MDc,SMDSc,APPc repo;

  %% external systems
  OIPO[OIPO offer / rates v8 / routing]:::ext
  TEMP[Temporal server<br/>REPRICING_TASK_QUEUE]:::ext
  KAFKA[Kafka + schema-registry]:::ext
  MONGO[(MongoDB — offered plans)]:::ext
  PG[(PostgreSQL — facilities / orders)]:::ext

  %% cross-repo edges (from _global_links.json)
  WIc -- "REST services.offer.base-url" --> OFFc
  WIc -- "REST services.order.base-url" --> ORDc
  OFFc -- "REST maersk.masterdata-base-url" --> MDc
  OFFc -- "REST facilities lookup" --> SMDSc
  ORDc -- "outbox_messages (DB)" --> SCHc
  SCHc -- "kafka iom-serviceplan-domain-event-topic" --> OFFc
  SCHc -- "kafka iom-serviceplan-domain-event-topic" --> ORDc
  SCHc -- "REST DELETE offer cleanup" --> OFFc
  SCHc -- "REST PATCH reprice" --> WIc

  %% external dependencies
  OFFc -. "offer/rates/routing" .-> OIPO
  OFFc -. persists .-> MONGO
  SCHc -. "repricing workflow" .-> TEMP
  SCHc -. publishes .-> KAFKA
  ORDc -. "consumes booking/SP/customer events" .-> KAFKA
  SMDSc -. stores .-> PG
  ORDc -. stores .-> PG
```

## Cross-repo edge legend (`_global_links.json`)

| From | To | Protocol | Topic / endpoint |
|---|---|---|---|
| iom-web-integrator | iom-offer-service | rest | `services.offer.base-url` (offer search / reprice / offered-plan) |
| iom-web-integrator | iom-order-service | rest | `services.order.base-url` (/v3/service-plans CRUD/status/search) |
| iom-offer-service | iom-master-data | rest | `${maersk.masterdata-base-url}` (location/charge/commodity/sales/container) |
| iom-offer-service | smds-facilities-service | rest | facilities lookup |
| iom-order-service | iom-order-service-scheduler | outbox-db | `outbox_messages` table |
| iom-order-service-scheduler | iom-offer-service, iom-order-service | kafka | `iom-serviceplan-domain-event-topic.local.v1` (EventNotification) |
| iom-order-service-scheduler | iom-offer-service | rest | `DELETE offered-service-plans?numberOfDays=` (offer cleanup) |
| iom-order-service-scheduler | iom-web-integrator | rest | `PATCH /service-plans/{n}/reprice` |

> `iom-application` is a shared compile-time library (no runtime cross-repo edge). It appears in
> `workspace.yml` but not in `_global_links.json`.
