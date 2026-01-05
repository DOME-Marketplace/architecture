---
title: Overview
description: An introduction to the digital identity system in DOME.
weight: 30
---

## Main concepts

- DOME uses electronic mandates for authentication of employees acting on behalf of organizations
  - The legal representative of the organization creates a mandate for each employee, delegating specific powers so they can perform actions on the DOME platform
- The eMandates are represented in machine-readable format (EUDI EAAs)
  - We use Verifiable Credential format, compatible with the EUDI Wallet (and the future Business Wallet)
- The employee presents the mandate when authenticating in DOME
  - The employee uses a Business Wallet to receive and present the mandate
- The eMandates are signed with an eIDAS digital certificate, issued by a QTSP.
  - We use ETSI/eIDAS JADES signatures (JSON Advanced Electronic Signatures)
- The signatures can be made by different actors:
  - Directly by the legal representative/organization (if they have a certificate issued by a QTSP)
  - Delegated to a Trusted Entity, which must be trusted by DOME (DOME has one, but others can be used)
- The eMandates use a specific ontology for representing the powersExpiration time on or after which the JWT MUST NOT be accepted for processing. In a M2M flow, this JWT is used only once and the client generates the JWT immediately before using it to call the token endpoint of the VCVerifier, with no human intervention or intermediate complex processes. The expiration time MUST be set as low as possible while allowing network delays, the major component that may affect this parameter. For example, 10 seconds, which is more than enough in most situations. In case of bad network conditions, the authentication can be retried with a new JWT. This is important for Replay protection, while simplifying management of unique jti claims in VC Verifier.

## The electronic mandate (eMandate)
An electronic mandate (eMandate) is a digital representation of a legal authorization given by one entity (the mandator) to another (the mandatary) to perform specific actions or represent them in certain contexts. In the DOME platform, eMandates are crucial for enabling employees to act on behalf of their organizations.

A good way to understand the specific usage of eMandates in DOME is with a well-know example.

### An example of a mandate and its representation in electronic form

To participate in the [EU Funding & Tenders Portal](https://ec.europa.eu/info/funding-tenders/opportunities/portal/screen/home), organizations must appoint a LEAR.
The LEAR (Legal Entity Appointed Representative) is a person, usually an administrative staff member in the central administration, appointed by the legal representative of the organisation (CEO, rector, Director-General, etc.). His/her tasks are to manage the legal and financial information of the organisation in the Participant Register on the Funding & Tenders Portal and to provide and update the list of persons in his/her organisation who are authorised to sign grant agreements (LSIGN) or financial statements (FSIGN).

This appointment is formalized with the LEAR Appointment Letter, which is effectively a mandate. An overview of the LEAR Appointment Letter is shown in the following figure.

{{< fig src="lear_letter_all.drawio.png" caption="LEAR Appointment Letter" >}}

There are three sections of interest in that Letter: 1) the identification of the organization and the legal representative; 2) the identification of the appointed employee (the LEAR); 3) the description of what the LEAR must/can do. These sections correspond to the three figures of Mandator (Legal Representative), Mandatee (LEAR), and delegated Powers.

The following figure focuses on the identification of the legal representative (which includes the identification of the organization), and the electronic representation in JSON format.

{{< fig style="width:100%" src="lear_representative.png" caption="Identification of Legal Representative" >}}

The next figure focuses on the identification of the employee who will be appointed as LEAR, and its representation in JSON.

{{< fig style="width:100%" src="lear_employee.png" caption="Identification of Employee" >}}

Finally, the next figure focuses on the formal description on the powers that the LEAR must have, and its representation in JSON format.

{{< fig style="width:100%" src="lear_power.png" caption="Delegated Powers" >}}

### The mandate in DOME

The above can be generalized to map the different sections of the LEAR Appointment Letter to the DOME eMandate, as shown in the following figure. This mapping ensures that all relevant information from the traditional mandate is accurately translated into its digital equivalent, facilitating automated processing and verification within the DOME platform.

You can see that the eMandate has an object called `Mandate`, which contains the `Mandator`, `Mandatee`, and `DelegatedPowers` objects, mirroring the structure of the LEAR Appointment Letter. You can also see that we use an eIDAS signature to sign the eMandate. This signature not only ensures its authenticity and integrity, but it also provides linkage to the real-world identity of the organization that appointed the LEAR, providing a proper level of legal certainty that other types of signatures can not provide.

{{< fig style="width:100%" src="mapping_mandate.png" caption="Mapping of the LEAR Appointment Letter to a DOME eMandate" >}}

We will talk now about the properties of the signature. But first, let's talk about the Trust Framework that we use in DOME

### The Trust Framework

The DOME Trust Framework relies upon the principles of eIDAS (electronic IDentification, Authentication and trust Services) Regulation, which provides a legal framework for electronic identification and trust services across the European Union. This framework ensures a high level of security, interoperability, and legal certainty for electronic transactions.


We do not have any CA (Certification Authority) in our ecosystem, and we do not issue any type of X.509 certificate. We rely on the eIDAS certificates issued by the QTSPs, so our trusted roots are the ones in the eIDAS trusted lists of qualified trust service providers and the services provided by them (https://digital-strategy.ec.europa.eu/en/policies/eu-trusted-lists).

{{< fig style="width:80%" src="eidas_dashboard.png" caption="Trusted Lists of Qualified Trust Service Providers" >}}

Any organization can obtain a certificate from any QTSP in any EU country issuing certificates for signatures/seals. Organizations are not limited to QTSPs in its country: many QTSPs provide remote identification services for organizations in other countries.

The chain of trust is represented in the following figure.

QTSPs have the legal mandate to verify the identity of the organization and of the legal representative against the Authentic Sources.

Not limited to private companies (in business registries), but available to most types of organizations (public administrations, associations, etc)

{{< fig style="width:80%" src="chain_of_trust.png" caption="Chain of Trust" >}}




### Key characteristics of eMandates in DOME:

- **Digital Representation:** eMandates are entirely digital, eliminating the need for physical documents and streamlining processes.
- **Legal Authorization:** They carry legal weight, ensuring that actions performed by the mandatary on behalf of the mandator are legally binding.
- **Specific Powers:** Each eMandate clearly defines the scope of authority granted, detailing the specific actions the mandatary is authorized to perform.
- **Machine-Readable Format:** eMandates are structured in a machine-readable format (EUDI EAAs using Verifiable Credentials), enabling automated processing and verification.
- **eIDAS Signatures:** eMandates are secured with eIDAS digital certificates, ensuring authenticity and integrity.
- **Flexible Issuance:** They can be signed directly by the legal representative or delegated to a trusted entity.
- **Ontology-based Powers:** The powers granted within an eMandate are defined using a specific ontology, allowing for precise and standardized representation.
- **Revocability:** eMandates can be revoked by the mandator, providing control and flexibility.
- **Interoperability:** DOME eMandates are designed to be interoperable with other systems and platforms that support EUDI EAAs and Verifiable Credentials.
- **Auditability:** All actions performed using an eMandate are logged, providing a clear audit trail.
- **Decentralized Trust:** While DOME provides a trusted entity for delegation, the architecture supports decentralized trust models, allowing other trusted entities to be used.
- **Granular Permissions:** The ontology-based powers allow for fine-grained control over the actions an employee can perform.
- **Lifecycle Management:** eMandates have a defined lifecycle, including issuance, activation, suspension, and revocation.
- **Non-repudiation:** eIDAS signatures and audit trails ensure that actions performed with an eMandate cannot be denied.
- **Transparency:** The use of open standards and machine-readable formats promotes transparency in how mandates are issued and used.
- **Efficiency:** Digital and automated processing of eMandates significantly reduces administrative burden and speeds up transactions.
- **Security:** Strong cryptographic mechanisms, including eIDAS signatures, protect eMandates from tampering and unauthorized access.
- **Cost-Effectiveness:** By automating processes and reducing reliance on paper, eMandates offer significant cost savings.
- **Legal Compliance:** Designed to comply with relevant legal frameworks, such as eIDAS, ensuring their legal validity across jurisdictions.
- **User-Friendly:** Simplifies the process of delegating authority and managing permissions for organizations and their employees.
- **Scalability:** The digital nature and automated processing capabilities of eMandates allow for efficient management of a large number of mandates and users.
- **Verifiability:** eMandates can be easily verified by relying parties, ensuring that the mandatary has the necessary authority.
- **Global Reach:** The adherence to open standards and international frameworks like eIDAS facilitates cross-border recognition and use of eMandates.
- **Reduced Fraud:** The strong security features and auditability of eMandates significantly reduce the risk of fraud and unauthorized actions.
- **Environmental Impact:** By reducing paper consumption and physical travel, eMandates contribute to a more sustainable and eco-friendly approach to business operations.
- **Interoperability with EUDI Wallet:** Seamless integration with the European Digital Identity Wallet for secure and standardized mandate management.
- **Future-Proof:** Built on open standards and evolving digital identity frameworks, ensuring long-term relevance and adaptability.
- **Streamlined Onboarding:** Facilitates quicker and more efficient onboarding of new employees and partners by digitizing the authorization process.
- **Enhanced Auditability:** Provides comprehensive logging and tracking of all mandate-related activities, supporting compliance and internal controls.