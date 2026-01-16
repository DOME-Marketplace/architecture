---
title: Overview
description: An introduction to the digital identity system in DOME.
weight: 5
---

DOME uses **electronic mandates for authentication of employees acting on behalf of organizations**. These electronic mandates are represented in machine-readable format compatible with the EUDI Wallet (and the future Business Wallet) to facilitate automated processing and verification, and eliminate the slow, cumbersome and error-prone manual processes associated with traditional paper-based mandates. 

DOME requires the eMandates to be **signed with an advanced signature or seal**, using an eIDAS qualified certificate issued by a QTSP (qualified trust services provider). This provides a level of legal certainty equivalent to any other business document signed in the same way, which is adequate for the overwhelming majority of business processes, including the ones in the DOME ecosystem.

In terms of eIDAS2, DOME accepts non-qualified EEAs (Electronic Attestation of Attributes), which facilitates its issuance and adoption:

- The eMandate can be **self-issued by the organization** which is the employer, using its HR database as the source of trust.
- Alternatively, it can be **issued by a trusted entity** (by DOME) which does not have to be a qualified trust services provider. 

DOME has extended the concept of eMandate for an employee to **machines and workloads**: organizations (or trusted entities) can issue EEAs to machines/workloads controled by the organization, providing a very powerful machine-to-machine authentication and access control mechanism. We will focus initially of the eMandate for employees, and later will describe its usage with machines and workloads.


## The electronic mandate (eMandate)

An electronic mandate (eMandate) is a digital representation of a legal authorization given by one entity (the mandator) to another (the mandatee) to perform specific actions or represent them in certain contexts. In the DOME platform, eMandates are crucial for enabling employees to act on behalf of their organizations.

A good way to understand the specific usage of eMandates in DOME is to describe a non-DOME well-know example and show how it is transposed to DOME.

### An example of a mandate

To participate in the [EU Funding & Tenders Portal](https://ec.europa.eu/info/funding-tenders/opportunities/portal/screen/home), organizations must appoint a **LEAR**.
The LEAR (Legal Entity Appointed Representative) is a person, usually an administrative staff member in the central administration, appointed by the legal representative of the organisation (CEO, rector, Director-General, etc.). His/her tasks are to manage the legal and financial information of the organisation in the Participant Register on the Funding & Tenders Portal and to provide and update the list of persons in his/her organisation who are authorised to sign grant agreements (LSIGN) or financial statements (FSIGN).

This appointment is formalized with the LEAR Appointment Letter, which is effectively a mandate. An overview of the LEAR Appointment Letter is shown in the following figure.

{{< fig src="lear_letter_all.drawio.png" caption="LEAR Appointment Letter" >}}

There are four sections of interest in that Letter:
1) the identification of the organization and the legal representative;
2) the identification of the appointed employee (the LEAR);
3) the description of what the LEAR must/can do;
4) the signature of the document.

The first three sections correspond to the figures of Mandator (Legal Representative), Mandatee (employee appointed as LEAR), and delegated Powers that the employee has in the Portal.

The following figure focuses on the identification of the organization and the legal representative, and provides an example representation in JSON format.

{{< fig style="width:100%" src="lear_representative.png" caption="Identification of Organisation and Legal Representative" >}}

The next figure focuses on the identification of the employee who will be appointed as LEAR, and an example representation in JSON.

{{< fig style="width:100%" src="lear_employee.png" caption="Identification of Employee" >}}

Finally, the next figure focuses on the formal description on the powers that the LEAR must have, and an example representation in JSON format.

{{< fig style="width:100%" src="lear_power.png" caption="Delegated Powers" >}}

### The mandate in DOME

The above can be generalized to map the different sections of the LEAR Appointment Letter to the DOME eMandate, as shown in the following figure. This mapping ensures that all relevant information from the traditional mandate is accurately translated into its digital equivalent, facilitating automated processing and verification within the DOME platform.

You can see that the eMandate has an object called `mandate`, which contains the `mandator`, `mandatee`, and `power` objects, mirroring the structure of the LEAR Appointment Letter. You can also see that we use an eIDAS signature to sign the eMandate. This signature not only ensures its authenticity and integrity, but it also provides linkage to the real-world identity of the organization that appointed the LEAR, providing a proper level of legal certainty that other types of signatures can not provide.

{{< fig style="width:100%" src="mapping_mandate.png" caption="Mapping of the LEAR Appointment Letter to a DOME eMandate" >}}

We will talk now about the properties of the signature. But first, let's talk about the Trust Framework that we use in DOME

## The Chain of Trust

The DOME Trust Framework relies upon the principles of eIDAS (electronic IDentification, Authentication and trust Services) Regulation, which provides a legal framework for electronic identification and trust services across the European Union. This framework ensures a high level of security, interoperability, and legal certainty for electronic transactions.

An overview of the chain of trust used in DOME is shown in the following figure. We elaborate on important details in the sections below.

{{< fig style="width:100%" src="chain_of_trust.drawio.svg" >}}

### The Trusted Root: EU Trusted Lists

According to the eIDAS Regulation, EU countries have the obligation to establish, maintain and publish trusted lists of qualified trust service providers (QTSPs) and the services provided by them. The services provided by a QTSP **is qualified only if it appears in a trusted list**.

There are different trust services that can be provided by the QTSPs, but the ones relevant for DOME are the issuance of qualified certificates for electronic signatures and seals.

In DOME we do not have any CA (Certification Authority) in our ecosystem, and we do not issue any type of X.509 certificate. We rely on the eIDAS certificates issued by the QTSPs, so our trusted roots are the ones in the eIDAS trusted lists (https://digital-strategy.ec.europa.eu/en/policies/eu-trusted-lists).

There are many QTSPs in the EU, where any organization can obtain a certificate from any QTSP in any EU country issuing certificates for signatures/seals. Organizations are not limited to QTSPs in its country: many QTSPs provide remote identification services for legal representatives of organizations in other countries.

{{< fig style="width:80%" src="eidas_dashboard.png" >}}

In DOME we trust on any QTSP which is included in the EU Trusted Lists.

### The QTSP and Authentic Sources

{{< fig style="width:80%" src="qtsp_authentic_sources.drawio.svg" >}}

In DOME we accept both seals and signatures because when a transaction requires a qualified electronic seal from a legal person, a qualified electronic signature from the authorised representative of the legal person is equally acceptable. For brevity, we will use the terms "organization certificate" or "certificate issued to an organization" to refer to both certificates for seals issued to an organization and certificates for signatures issued to a legal representative of the organization.

QTSPs issuing qualified certificates to organizations should implement the necessary measures in order to be able to establish the identity of the natural person representing the legal person to whom the qualified certificate is provided.
In order to do so, QTSPs may use different mechanisms, one of which may be to verify the identity of the organization and of the legal representative against the Authentic Sources.

This is a key property of the mechanism used in DOME: if a person presents a qualified organization certificate and proves control over the private key of the certificate, we automatically heve these benefits:
- We do not have to request any documents from the organization or the legal representative and verify them against the authentic sources (e.g., business registry), because the QTSP has already performed the checks, with a high level of assurance.
- We know that the person presenting the certificate is indeed the legal representative of the organization, because of the mechanism that was used to issue the certificate, which must ensure the sole control of the certificate.

In a pan-european context, those are cumbersome and lengthy processes if we only use "traditional" paper/PDF documents.

### Issuance of the mandate by the organization

{{< fig style="width:80%" src="issuance_of_mandate.drawio.svg" >}}

The next link in the chain of trust is the issuance of the eMandate by the organization.

In DOME we are satisfied with an advanced signature or seal performed with a qualified organizational certificate. In terms of eIDAS2, we in DOME accept an EAA and do not require a QEAA (but of course, we are happy to accept also QEAAs).

The overwhelming majority of current business transactions using digital signatures require advanced electronic signatures or seals, because qualified signatures or seals are more complex to obtain, and are not required in the legal framework for most transactions.

This enables a very powerful feature: the eMandate can be generated by the organization itself, using the eIDAS certificate to sign it, and then issued to the employee, who will use it to authenticate in DOME, without the need for the organization to integrate with DOME, thus simplifying the process for organizations and reducing administrative burden, and allowing for a more decentralized approach, which is a key aspect of DOME, and a significant advantage over traditional centralized systems.

We do not need any QTSP or Business Register to issue the eMandate. The organization is the "authentic source" for whether a person is an employee or not, and we "trust" the signature of a business document using an advanced signature with a qualified certificate.
The legal certainty (in case there is litigation) is actually higher than if we used "traditional" methods..

The world, however, is very complex and some organizations are not ready to issue themselves the eMandate. We will se in another part of this document how DOME provides alternative procedures to overcome these limitations, which are supposedly temporal until eIDAS2 is fully implemented and adopted in the EU.

### Authentication of the employee in DOME

{{< fig style="width:80%" src="authentication_of_employee.drawio.svg" >}}

The employee authenticates to DOME by presenting the eMandate using his/her EUDI-compatible Wallet (a Business Wallet in most cases).

Given that neither EUDI Wallets (for citizens) nor Business Wallets (for organizations) have been deployed yet in the EU, we in DOME provide since summer 2024 an implementation of a Business Wallet which organizations and its employees can use.

This Business Wallet is fully aligned with the eIDAS2 specifications, but we do not implement all of the requirements, only the ones which are required to implement the DOME use cases. For example, we do not require proximity flows and only support online flows.

We will talk more about the Business Wallet in another part of this document, but for now suffice to say that we have defined a "DOME profile" with the minimum requirements that Business Wallets have to comply in order to be able to be used in DOME.


### Summary: Key characteristics of eMandates in DOME:

- **Digital Representation:** eMandates are entirely digital, eliminating the need for physical documents and streamlining processes.
- **Legal Authorization:** They carry legal weight, ensuring that actions performed by the mandatee on behalf of the mandator are legally binding.
- **Specific Powers:** Each eMandate clearly defines the scope of authority granted, detailing the specific actions the mandatary is authorized to perform.
- **eIDAS Signatures:** eMandates are secured with eIDAS digital certificates, ensuring authenticity and integrity.
- **Non-repudiation:** eIDAS signatures and audit trails ensure that actions performed with an eMandate cannot be denied.
- **Flexible Issuance:** They can be signed directly by the legal representative or delegated to a trusted entity.
- **Ontology-based Powers:** The powers granted within an eMandate are defined using a specific ontology, allowing for precise and standardized representation.
- **Interoperability:** DOME eMandates are designed to be interoperable with other systems and platforms that support EUDI EAAs and Verifiable Credentials.
- **Auditability:** All actions performed using an eMandate are logged, providing a clear audit trail.
- **Decentralized Trust:** While DOME provides a trusted entity for delegation, the architecture supports decentralized trust models, allowing other trusted entities to be used.
- **Granular Permissions:** The ontology-based powers allow for fine-grained control over the actions an employee can perform.
- **Lifecycle Management:** eMandates have a defined lifecycle, including issuance, activation, suspension, and revocation.
- **User-Friendly:** Simplifies the process of delegating authority and managing permissions for organizations and their employees.
- **Scalability:** The digital nature and automated processing capabilities of eMandates allow for efficient management of a large number of mandates and users.
- **Global Reach:** The adherence to open standards and international frameworks like eIDAS facilitates cross-border recognition and use of eMandates.
- **Interoperability with EUDI Wallet:** Seamless integration with the European Digital Identity Wallet for secure and standardized mandate management.
- **Future-Proof:** Built on open standards and evolving digital identity frameworks, ensuring long-term relevance and adaptability.
- **Streamlined Onboarding:** Facilitates quicker and more efficient onboarding of new employees and partners by digitizing the authorization process.
