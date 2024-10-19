---
title: Access Node Database Decision
description: Choose the database for the Access Node in the DOME project.
weight: 10
---

# Access Node Database Decision

## Status

> What is the status, such as proposed, accepted, rejected, deprecated, superseded, etc.?

The current status of this decision is **proposed**.

## Context

> What is the issue that we're seeing that is motivating this decision or change?

The current Access Node instance three databases. A Redis database for the TM Forum API component, a PostgreSQL for the Context Broker and a PostgreSQL for the Replication Service. Actually, the team in charge of developing the fourth component of the Access Node wants to implement its own database. If we consider the current architecture, we will have four different databases in the Access Node. This could lead to a more complex architecture and more difficult to maintain because each one implements a single instance of a database.

```mermaid
graph TB
    marketplace[Marketplace]
    subgraph "Access Node"
        tm_forum_api[TM Forum API]
        redis[(Redis)]
        context_broker[Context Broker]
        postgis[(PostgreSQL)]
        replication_service[Replication Service]
        postgres[(PostgreSQL)]
        dlt_adapter[DLT Adapter]
    end
    blockchain[Blockchain]

    marketplace --> tm_forum_api
    tm_forum_api --> redis
    context_broker --> postgis
    replication_service --> postgres

    tm_forum_api --> context_broker
    context_broker --> replication_service
    replication_service --> dlt_adapter
    
    dlt_adapter --> blockchain
```

## Decision

> What is the change that we're proposing and/or doing?

We propose an approach to using a single database for all components of the Access Node. This approach will simplify the architecture and make it easier to maintain. Nevertheless, we need to consider that each component will need to implement its own schema in the database separating the responsibility of each component.

```mermaid
graph TB
    marketplace[Marketplace]
    subgraph "Access Node"
        tm_forum_api[TM Forum API]
        context_broker[Context Broker]
        replication_service[Replication Service]
        dlt_adapter[DLT Adapter]
        postgres[(PostgreSQL)]
    end
    blockchain[Blockchain]

    marketplace --> tm_forum_api
    tm_forum_api -->|Schema: schema_tm_forum| postgres
    context_broker -->|Schema: schema_context| postgres
    replication_service -->|Schema: schema_replication| postgres
    dlt_adapter -->|Schema: schema_dlt| postgres

    tm_forum_api --> context_broker
    context_broker --> replication_service
    replication_service --> dlt_adapter

    dlt_adapter --> blockchain
```

## Consequences

> What becomes easier or more difficult to do because of this change?

One of the difficulties could be that some of the current databases implement or have some bound configurations, and we will need to change the static configuration to external configuration. This could lead to a more complex configuration for the database. Nevertheless, we believe that the benefits of having a single database for all components outweigh the drawbacks.

