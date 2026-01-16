---
title: The LEARCredential
description: We describe here the LEARCredential, which is an electronic mandate for powerful authentication and access control in DOME and beyond.
weight: 10
---

The `LEARCredential` is a machine-readable legal document representing an electronic mandate (known also as Company Authorization Letter). In this context, the term `mandate` is used to describe the concept of **delegating specific permissions and powers to an employee of an organisation, enabling that employee to act on behalf of the company in a specific set of matters or tasks**.

We explicitly focus on the business environment and do not cover natural persons acting as citizens.

Instead of inefficient and insecure paper or PDFs, in DOME we use **Verifiable Credentials** to represent an eMandate. Given that we are in the EU, in DOME we use **eIDAS electronic signatures or seals** to make the credential a legal document with higher legal certainty than using other types of digital signatures (for example, a qualified electronic signature has in the EU the same legal validity as a handwritten signature).

In DOME, the specific powers being delegated are related to the interaction of an employee of an organisation with the DOME ecosystem, for example to authorise the employee perform onboarding, creating a Product Offering in the marketplace or contracting a product from a provider.

The LEARCredential (**L**egal **E**ntity **A**uthorised **R**epresentative **Credential**):
- is a Verifiable Credential that a legal entity (an organisation) issues to an employee (or subcontractor),
- includes the list of specific powers (or authorisations) that are delegated to an employee, so the employee can act on behalf of the legal entity
- can be used also as an **authentication mechanism**, eliminating the need for passwords or complex identity management systems
- the receiver of the credential can perform **access control** using the powers that are included inside the credential.

The figure below describes a high-level view of the LEARCredential.

{{< fig style="width:60%" src="learcredential-overview.png" caption="Composition of a LEARCredential" >}}

The data model of the LEARCredential is based in the [RPaM-Ontology](https://github.com/everis-rpam/RPaM-Ontology/wiki/Ontology-Development-Report), which is a project started in 2018 by the Directorate-General for Informatics (DG-DIGIT) of the European Commission, funded by the ISA Programme, to organise and support the development of an ontology about the **Representation of Powers and Mandates**, from now on the RPaM Ontology.

We adapt the results of that project, with simplifications and specialisations, to the concrete environment where we use the LEARCredential. To facilitate the job of the reader of this document, in some sections we copy literal sentences from the RPaM Ontology project, and adapt the texts to our requirements. However, the reader is encouraged to access the original documents for more details and understand the original approach, including the [RPaM Ontology Glossary](https://everis-rpam.github.io/Glossary.html).




## The Mandate
According to the RPaM Glossary, a "mandate is a record that describes the terms under which a mandator grants a representation power to a mandatee".

In DOME, the mandate is composed of three related objects: `mandator`, `mandatee`, `power` and `signer`. The mandate object is signed or sealed with an advanced or qualified signature or seal using an eIDAS certificate.


### Mandator

The object mandator identifies the employee of the company who is delegating a subset of her powers on the mandatee.

The mandator is either:

- a legal representative of the company, according to the official records associated to the incorporation of the organisation (e.g., the business registry of the country of incorporation); or

- an employee who is a mandatee in another mandate where the mandator is a legal representative of the company. We do not support more than two levels of delegation.


### Mandatee

The mandatee is the person granted with the power to represent (and act as) the company in some specific actions with third-parties. The powers granted to the mandatee must be a subset of the powers of the mandator. For example, an employee (the mandatee) can be empowered by the legal representative of the company (the mandator) to perform the onboarding process in DOME.

The object mandatee identifies the employee on whom a subset of powers is delegated. The mandatee object contains:

- A set of attributes of the employee which are required by the specific use case where the LEAR Credential will be used, for example to onboard in the DOME ecosystem and for creating a ProductOffering in the marketplace. Those attributes can be considered equivalent to the fields that would be filled in a form when a "classical" PDF document would be used to authorise an employee.

- A public key associated to the employee and where the employee is the sole controler of the associated private key. This is required to enable the use of the LEARCredential containing the mandate as an efficient, scalable and secure authentication and authorisation mechanism.

The private key controlled by the employee is used to prove to Relying parties receiving the LEARCredential that the holder and presenter of the credential is the same person identified in the mandatee object. We support two mechanisms for the public key in the mandatee object:

- Using a did:key where the employee controls the private key associated to the did:key.
- Using an eIDAS certificate owned by the employee. This is very rare today (end of 2024), but will become more common when eIDAS2 and the EUDI Wallet is adopted.

### Signer

The Signer is either the Mandator or a third-party that attests that the Mandator really delegated the powers to the Mandatee. The Signer is the entity that performs an advanced or qualified signature or seal using an eIDAS certificate.

The Signer is the entity that has to be trusted by the receiver of the LEARCredential.


### Power

This object is a list of each specific power that is delegated from the mandator to the mandatee. The powers must be concrete and as constrained as possible, and must follow a taxonomy with the semantics well specified.

In DOME, we have specified a power taxonomy targeted at the interactions with the DOME Federated Marketplace. This means that the actions are well defined, homogeneous and standardised for the ecosystem. We are basically replacing the current mechanisms for Mandates (e.g., paper or PDF) with a more efficient, machine-processable representation in the form of a Verifiable Credential.

Our Power Taxonomy could be generalised to other actions involving private sector companies, but it is out of scope of this version of the document.


The `power` object is an array where each element is a power that is delegated from the Mandator to the Mandatee. The fields of each power object are:

- `id`: REQUIRED. The identifier of the power, which must be unique in the context of the Credential where it is included. It can be used as a reference when performing access control, for example in the audit records to identify the specific power that was used to grant access to some protected resource.

- `powerSource`: OPTIONAL. The Mandator draws the power from one (or more) sources of power, e.g., 1) a concrete Legislation; 2) a piece of evidence (as another Mandate, in which case the mandator was a mandatee in that other mandate) and/or 3) a Natural Person with a specific profession that invest him/her with the authority to order to a mandator with a specific role the creation of a mandate, e.g. a judge authorising a civil servant (a `Mandator` that is a Natural Person with the appropriate role) to create a mandate for a relative to represent an incapacitated person).

  In DOME, there are three cases with specific sources of power:

  - When the Mandator is a legal representative of the company, and the LEARCredential is signed with the eIDAS certificate of representation of the legal representative. In this case, the `powerSource` claim can be ommitted as it is implicit in the eIDAS signature. Alternatively, the `powerSource` claim is an object with the following fields:
    
    - `type`, with value `eulaw`.
    - `evidence`, with value ["https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32014R0910"](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32014R0910).

  - When the Mandator is a natural person who is the Mandatee in another LEARCredential. The `powerSource` claim is an object with the following fields:

    - `type`, with value `LEARCredential`.
    - `format`, with the value `jwt_vc_json`.
    - `evidence`, with the LEARCredential where the Mandator is a Mandatee, in `jwt_vc_json` format.

  - When the Mandator is a legal representative of the company, but does not have an eIDAS certificate of representation, the Mandator can not sign the LEARCredential. In this case, there must be a trusted third-party which attests that the Mandator is effectively a legal representative of the company. This trusted third-party must perform the required due diligence to confirm the relationship, and then sign the credential with its eIDAS certificate.

    In DOME, one of the trusted third-parties (trusted by DOME) is the DOME Operator itself, where the employees dedicated to the onboarding of participants perform the manual checks on the documentation provided by the Mandator/Mandatee, and then create and sign the LEARCredential.

    In this case, the `powerSource` claim is an object with the following fields:
    
    - `type`, with value `attestation`.
    - `evidence`, with value the eIDAS certificate of the attester.

    In this case, the `mandate` object must contain an additional object called `attester`, identifying the entity that makes the attestation and with the same fields as the `mandator` object in the case where the Mandator signs the LEARCredential with an eIDAS certificate.


- `type`: REQUIRED. The type of power, which in DOME may be:

  - `Domain`: The mandatee has access to the services provided by one or more organisations where the services have been classified as belonging to that domain. Access to the services is subject to the restrictions defined by `action` and `function` below. The classification of services is arbitrary and is defined by the service providers. A typical scenario is when some service providers agree on a classification of the services that they provide collaboratively in the context of an ecosystem (like DOME), and the possible domains are made public, including the mapping to a given set of services in each provider.
        
  - `Organization`: The mandatee has access only to the services provided by one or more organizations, identified specifically in the power.

  Other types of powers can be defined, making the system extensible.

- `domain`: REQUIRED when the `type` claim has the value `Domain` or `Organization`. It is an array with the names of the Domains or Organisations where the Mandatee is authorised to interact with this Mandate.

  The names must be unique (e.g. via a namespace) to avoid potential clashes in different ecosystems.
                
  In DOME, for the powers of onboarding in DOME and for creating a `ProductOffering`, the `domain` claim must be an array with a single item with the value `DOME`.

- `function`: A string specifying the name of the function that the Mandatee can perform. The definition of the possible functions is done by the Relying Party (the entity with which the Mandatee will interact with the LEAR Credential).
    
  In DOME, two essential functions for Cloud Service Providers (CSP) accessing the marketplace are `Onboarding` and  `ProductOffering`, which enable the Mandatee to use the services provided by DOME to onboard organisations in the ecosystem and (if it is onboarded previously) to create a ProductOffering, respectively.

- `action`: An array with the concrete actions belonging to the function that the Mandatee is allowed to execute.
    
  In DOME, the possible actions for CSPs accessing the marketplace are:
        
  - `Execute` when `function` is `Onboarding`.
  - Any combination of `Create`, `Update` and `Delete` when `function` is `ProductOffering`.
