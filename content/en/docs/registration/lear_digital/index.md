---
title: LEARCredential (digital process)
description: >
  Creating a LEARCredential for an employee, the automated and self-service way.
weight: 20
---

## Go to the DOME Issuer portal

The DOME Issuer portal is located at https://dome.mycredential.eu/ and below is an image of the screen.

![DOME Issuer portal](../dome_issuer_home.png)

The site is intended for Legal Representatives of companies who want to issue one or more LEARCredentials to one or more employees of the company. A LEARCredential is a type of Verifiable Credential which enables an employee, nominated by a legal representative, to act on behalf of an organisation with restricted powers with respect to third-parties.

{{< fig src="featured-dome_issuer_home.png" caption="An elephant at sunset" >}}

The issuer (the creator) of the LEARCredential must be a legal representative of the company. The legal representative will sign the LEARCredential with an eIDAS digital certificate, which can be either a personal one or a certificate of representation.

{{< fig link="https://www.google.com" src="featured-dome_issuer_home.png" caption="An elephant at sunset" >}}


The receiver of the LEARCredential (both the subject and holder of the credential) can be any employee (or contractor) of the company. The legal representative will delegate a restricted set of powers to that person. Those restricted powers are included inside the credential and can be verified by any Relying party to whom the holder presents the LEARCredential.

If you are a legal representative of the company and you have an eIDAS certificate installed in your PC, click on the yellow button to start the process:

![Go to Issuer](/docs/onboarding/login_as_legal_representative.png)

## Select the eIDAS certificate to use

Once you click the button, you will be directed to the page of the DOME Issuer to start creating LEARCredentials.

The browser will request that you select the certificate to use, with a screen like the following:

![Select certificate](/docs/onboarding/select_certificate.png)

## Register for the service

The first time you enter, you must register and verify your company email, using the following screen.

![Register email](/docs/onboarding/register_email.png)

Enter your email address and click the button "Register (if this is the first time)".

You will receive an email requesting confirmation, which is needed before you can logon in the system.

If you do not receive the email (from support@mycredential.eu), please check in spam just in case your email client has automatically classified it as spam.

Once you have verified your email you can logon in the system using the same screen as above.

## List of LEARCredentials

After logging in for the first time, you will see an empty screen like the following. Click on the button to create a new LEARCredential.

![List of Offers 1](/docs/onboarding/list_of_offers_1.png)

## LEARCredentialForm

A form to fill the data of the LEARCredential appears, like the following:

![LEARCredential form](/docs/onboarding/credential_form_1.png)

The form has the following sections:

### Mandator

The Mandator is the person delegating a restricted set of powers onto an employee (the Mandatee). This part of the form is pre-filled with information coming from the eIDAS certificate that was used to enter into the application, plus the email address that was registered and verified.

You do not have to enter anything else in this section.

![Mandator](/docs/onboarding/mandator.png)

### Mandatee

The Mandatee is the employee (or subcontractor) who receives a restricted set of powers to act on behalf of the company with third-parties. In our case, the employee will be empowered to perform the Onboarding process in DOME, and maybe to create Product Offerings in the Marketplace.

You have to fill the data corresponding to the employee that you want to nominate to perform the Onboarding process. After creating the LEARCredential, the employee will receive a notification in the email address that you entered in the form.

![Mandatee](/docs/onboarding/mandatee.png)

### Powers

These are the specific powers that the Mandator (you) is delegating on the Mandatee (the employee).

The LEARCredential is a general purpose electronic Mandate, but in our case we need only two powers:

- Execute the Onboarding process in DOME
- Create/Update/Delete one or more Product Offerings in the DOME Marketplace

![Powers](/docs/onboarding/powers.png)

You can assign each power to a different employee (with one LEARCredential for each employee), or both powers to the same employee (with one LEARCredential including both powers). You can even generate more than one LEARCredential with the same or different powers to one or more employees. This is up to you and how you want to distribute the powers among your employees.

The Mandate in the form of a Verifiable Credential bechaves like its paper counterpart with regards to these properties.

For Onboarding, make sure that the employee receives at least the Onboarding power:

![Onboarding](/docs/onboarding/powers_onboarding.png)

## Create a first version of the LEARCredential

Press the "Create" button at the bottom of the form. This will take you to a screen with the machine-readable representation of the new LEARCredential. Do not worry, you do not have to understand this, but if you do (or somebody in your company) it is a good way to see the actual contents that will go inside the LEARCredential, before sending it to the employee.

![JSON model Onboarding](/docs/onboarding/json_credential_offer.png)

Click the button at the bottom of the screen to save the draft version of the credential and send a notification to the target employee via email.

## List of created LEARCredentials

You are taken back to the list of LEARCredentials that you have created. If this is the first time that you have created a credential, thee screen looks like the following.

![List of one credential](/docs/onboarding/list_of_offers_one_credential.png)

The button "View" at the left of every item allows you to review the credential, and also to send a reminder email to the target employee, in case the employee has not yet retrieved the credential.

LEARCredentials have three states:

- **offered**: A draft version of the credential has been created with all required information about Mandator, Mandatee and the Powers of Representation. A notification via email has been sent to the employee who will receive the powers. The name `offered` denotes that the employee has to accept the credential and load it to the Wallet of th eemployee.
- **tobesigned**: The employee has accepted the LEARCredential, after reviewing the information contained in it. This step is necessary because the LEARCredential contains cryptographic material which will allow the employee to authenticate to DOME, and that cryptographic material can only be generated by the Wallet of the employee, to ensure the solo control of the private key.
- **signed**: After the employee accepts the credential, you have to sign it with your eIDAS certificate. After your signature, the credential is signed and ready to be loaded by the employee in her Wallet and use it.

## The employee reviews and confirms the LEARCredential

The employee receives an email notification like the one below.

![Employee email notification initial](/docs/onboarding/employee_offer_notification.png)

When clicking on the button, the employee is directed to the DOME Issuer portal to review and (possibly) confirm the LEARCredential.

The screen that the employee sees is similar to the one below.

![Employee Issuer portal initial](/docs/onboarding/employee_credential_accept_portal.png)


![Wallet review offer](/docs/onboarding/wallet_review_offered_credential.png)


