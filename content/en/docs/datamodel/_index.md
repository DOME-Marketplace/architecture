---
title: Data Model
description: Using the TM Forum Open APIs and data model for interoperability and federation of marketplaces.
weight: 20
---

In a decentralised federated ecosystem like DOME, interoperability is a key requirement because we need the different marketplace instances in the DOME ecosystem to exchange catalog data and use the information for their users.

To achieve that, it is useful to think of interoperability at different layers:

- **Trust and digital identities level**. A prerequisite for the exchange of data in DOME is that all participants trust to each other with a given level of assurance, and that they use a common digital identity system so they know who is who.

- **Data model and semantic level**. If the participants do not share a common semantic data model, it is extremely difficult to engage in automated and efficient data exchanges.

- **Technical communications mechanisms level**. Eventually, data must be echanged via common communication mechanisms, and the parties must agree on the standards used on this level to be able to connect and exchange the data.

We are interested in this section on the data model and semantic level, as the following figure describes.

{{< fig src="data_for_replication.png" caption="The role of common data model in replication." >}}


In the DOME ecosystem, each marketplace instance uses most probably a different data model, depending on the actual implementation of the marketplace software. Trying to force all marketplace operators to use a single software for their marketplace instances is not reasonable. 

There are many implementations of marketplaces out there, some of them Open Source. Of course, each implementation has its own data model, with varying degrees of sophistication. However, we have found that trying to use the data model defined by one of those implementations is also not reasonable.

In DOME we have chosen to focus on the data model, not on the software implementations of marketplaces. Instead of inventing the wheel, we wanted an existing data model:

- designed by a community as large as possible, with proper governance model
- suited for marketplace catalog data
- focused on open data models and open APIs for interoperability across complex heterogeneous ecosystems
- as mature as possible, and as widely adopted as possible, with as many different implementations by different entities as possible

We chose the [TM Forum's Open API project](https://www.tmforum.org/oda/open-apis/), which complies with all those requirements, and there is a big community around the data model and related APIs. See for example the dashboard for July 2024 in the figure below.

{{< fig src="tmforum-api-dashboard-2024-08.png" caption="TM Forum data model and APIs ecosystem." >}}

For the replication of catalog data, we use in DOME a subset of the whole set of TM Forum APIs, specifically the ones related to the Product APIs, as described below.

{{< fig src="tmforum-apis-used.png" caption="The subset of APIs used in DOME." >}}

The following diagram provides a simplified description of the main entities involved in the actual data model used. The model has provisions for extensions, and in DOME we have made use of that extensibility to implement things related to Verifiable Credentials and trusted replication, for example.

{{< fig src="tmforum-datamodel.png" caption="The main entities in the DOME data model." >}}