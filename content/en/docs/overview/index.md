---
title: Overview
description: Overview of the DOME technical architecture.
weight: 10
---

The DOME ecosystem consists of a set of federated marketplaces, replicating the catalogue data among them in a secure way.

The diagram below provides an overview of the core components of DOME.

{{< fig src="YUMKET technical architecture.drawio.png" caption="The core components of DOME" >}}

In July 4th 2024, DOME entered into production with a single marketplace instance operated by the DOME Consortium (the dotted rectangle on the left). In the coming months it is expected that other instances of marketplaces, operated by different entities, will start integrating and federating among them to create the DOME ecosystem (one of those federated instances is shown on the right of the diagram).

The replication of catalog data is implemented with a layered architecture:

Marketplace Business layer
: Even though the different instances may use natively different data models, they have to agree on a common data model and semantics. DOME uses the standard [TM Forum's Open APIs](https://www.tmforum.org/oda/open-apis/) and associated data model.

TM Forum Open APIs have been widely adopted by the industry as a standard interoperability method, with more than 640,000 downloads by 39,000 software developers from 2,500 organizations.

Over 70+ REST-based, Event driven and Domain Specific Open APIs have been collaboratively developed by TM Forum members working within the Open API project. This team creates a full set of Open API assets for members to implement Open APIs - from guidelines (REST API Design Guidelines), to specification, to usable code, through to the compliance test kits - see the Open API directory for more details.

Catalog Replication layer
: This is where the actual replication of the data happens. All instances have a store with the relevant catalog data in the standard TM Forum format. The replication logic in each instance talk to each other with the help of a byzantine fault tolerant Publish/Subscribe event bus implemented by the lower layer.

Trusted Publish/Subscribe layer
: This layer implements a Publish/Subscribe event bus, where the lifecycle events of the relevant catalog objects are managed. For example, when a new product offering is published in one of the instances of the marketplaces, an event is published in the bus. All other instances which are subscribed to that type of event will receive the event and if the necessary conditions are met, the replication logic in that instance will retrieve the associated data from the origin instance.

The Publish/Subscribe event bus is Byzantine Fault Tolerant (BFT), which means that is continues working even in the presence of malicious actors who control one or more servers in the infrastructure (up to a given threshold given by the actual properties of the infrastructure).