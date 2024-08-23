---
title: Authentication
description: Authentication in DOME with the LEARCredential, both for Human-to-Machine (H2M) and Machine-to-Machine (M2M) use cases.
weight: 20
---

DOME uses Verifiable Credentials for authentication and access control of the users interacting with the DOME services.

In this document we describe **how you can integrate the DOME authentication mechanism in your own applications**. It is very easy.

You do not need to know the underlying technical details to use it, but if you are interested you can look [here](https://dome-marketplace.github.io/powers-of-representation/index.html).


There are two types of users that can authenticate to DOME services:

- Employees of companies using DOME services. We call this the Human-to-Machine (M2M) flows.
- Applications or machines owned or operated by an organisation, accessing the DOME services. We call this the Machine-to-Machine (M2M) flows.

We first explain the H2M flow: how your application can authenticate employees of companies (including your own company, of course). Later we describe the M2M flow.

# Human-to-Machine (H2M) authentication flow

{{< fig src="DOMEAuthentication-H2M-flow-overview.drawio.png" caption="Two applications authenticating with Verifiable Credentials" >}}

DOME provides a component called the **VCVerifier** which makes very easy for applications to integrate authentication with Verifiable Credentials. If the application programmer knows how to use <a href="https://en.wikipedia.org/wiki/Social_login">Social Login</a>, then she already knows who to integrate authentication with Verifiable Credentials.

For the Application, the VCVerifier is just an OpenID Provider (OP), connecting to it using the standard <a href="https://openid.net/developers/how-connect-works/">OpenID Connect</a> protocol.

The Application does not need any knowledge about how to talk to the Wallet to receive the Verifiable Credential from the user. This complexity is hidden from the application by the VCVerifier. The Application never talks to the Wallet, only to the VCVerifier.

Once the VCVerifier has received the Verifiable Credential from her Wallet and performed a set of verifications (according to a defined policy), it sends to the Application both the Verifiable Credential and an Access Token that the Application can use to access protected resources as in any other OpenID Connect flow.

## Parameters of the VCVerifier

You have to tell your Application some things before it can talk to the VCVerifier.

When talking to the VCVerifier, your Application MUST use the OIDC **Authorization Code Flow**. No other OIDC flows are supported.

Your Application must use the following endpoints of the VCVerifier during the flow:

- **Authorization Endpoint**: https://verifier.dome-marketplace-prd.org/auth
- **Token Endpoint**: https://verifier.dome-marketplace-prd.org/token
- **UserInfo Endpoint**: https://verifier.dome-marketplace-prd.org/userinfo

## Starting the Authentication Request

Before any authentication can take place, your Application has to be registered with the VCVerifier. This is done during the onboarding process in DOME, or at any time later, contacting the onboarding team.

As part of the registration, you will receive a **Client Identifier** and a **Client Secret**, that your Application must use according to the OIDC standard.

Typically, you will display to the user a Login button, and when the user clicks the button, your Application will redirect the user to the VCVerifier, passing some parameters as per the OIDC specifications. This is called an **Authentication Request**.

The following is an example HTTP 302 redirect response by the Application, which triggers the browser of the user to make an Authentication Request to the Authorization Endpoint of the VCVerifier (with line wraps within values for display purposes only).

```http
HTTP/1.1 302 Found
Location: https://verifier.dome-marketplace.org/auth?
    response_type=code
    &client_id=did:key:wejkdew87fwhef9833f4
    &request_uri=https%3A%2F%2Fdome-marketplace.org%2Fapi%2Fv1%2Frequest.jwt
    %23GkurKxf5T0Y-mnPFCHqWOMiZi4VS138cQO_V7PZHAdM
    &state=af0ifjsldkj&nonce=n-0S6_WzA2Mj
    &scope=openid%20learcred
```

Note the following:

- The Authentication Request MUST use the **Authorization Code Flow**, by specifying **response_type=code**. This means that all tokens (including the claims of the Verifiable Credential) are returned from the Token Endpoint.

- The **client_id** is the one assigned to your Application when registered in the VCVerifier.

- The Authentication Request MUST include the scope **learcred**, which tells the VCVerifier that the Application is requesting a LEARCredential (the one used in DOME for authentication). This scope is added to the OIDC compulsory scope **openid**.

- The Authorization Request MUST use the **request_uri** parameter which enables the request to be passed by reference, as described in [section 6.2. Passing a Request Object by Reference](https://openid.net/specs/openid-connect-core-1_0.html#RequestUriParameter) of the OpenID Connect spec.

- **state** is used by your Application to match this request with the future reply, in order to support multiple users at the same time.


## Receiving the Verifiable Credential in your Application

After the execution of the Authorization Code Flow, your Application can receive the Verifiable Credential in two ways:

- As an additional claim in the Access Token from the VCVerifier Token Endpoint. When your Application calls the Token Endpoint, it receives a normal access token which includes the claim **verifiableCredential**, which is the LEARCredential in JSON format.

- As an additional claim in the response from the Userinfo endpoint. The claim name is the same as in the access token: **verifiableCredential**.

The contents of the LEARCredential can be used by the Application to perform not only authentication but also authorization (access control). For example, using the Powers of the User which are included in the LEARCredential.

# Machine-to-Machine (M2M) authentication flow

{{< fig src="DOMEAuthentication-M2M-flow-overview.drawio.png" caption="A server authenticating with Verifiable Credentials" >}}

The VCVerifier component of DOME support also the M2M flows. The figure above shows a server application using the VCVerifier to exchange a Verifiable Credential for an Access Token, and then using the token to access protected resources. The M2M (machine-to-machine) flow for authentication is simpler than the one for H2M, because there is no user authentication involved.

The credential used by the Application for authenticating to the VCVerifier is the LEARCredential that was generated for the Application when registering it to the VCVerifier (the same process as in the H2M flow).

The M2M flow uses the **Client Credentials Grant**, following the [OAuth 2.1 IETF draft (12 July 2024)](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-10), which among other things takes into account the [OAuth 2.0 Security Best Current Practice](https://oauth.net/2/oauth-best-practice/) and consolidates several new RFCs that are relevant for our use case.


## Authenticating and receiving an Access Code

The M2M flow uses a Token Endpoint specifically for M2M:

- **M2M Token Endpoint**: https://verifier.dome-marketplace-prd.org/token_m2m

This is an example request to the M2M Token Endpoint:

```http
POST /token_m2m HTTP/1.1
Host: verifier.dome-marketplace.org
Content-Type: application/x-www-form-urlencoded

    grant_type=client_credentials&
    client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&
    client_assertion=eyJhbGciOiJS[...omitted for brevity...]cC4hiUPo
```

The M2M flow MUST use the Client Credentials Grant (**grant_type=client_credentials**), because there is no user involved.

For authentication, the Application MUST use the **private_key_jwt** method, as described in section [9. Client Authentication](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) of the OIDC standard.

The value of the **client_assertion_type** parameter MUST be **urn:ietf:params:oauth:client-assertion-type:jwt-bearer**, and the authentication token MUST be sent as the value of the **client_assertion** parameter.

The authentication token is created by the Application using the LEARCredential. The JWT in the **client_assertion** contains:

- **iss**: REQUIRED. This MUST contain the client_id of the OAuth Client, which is the `did` assigned to the machine in the LEARCredential of the machine. For example **did:key:wejkdew87fwhef9833f4**

- **sub**: REQUIRED. This MUST contain the same value as the `iss` claim.

- **aud**: REQUIRED. The aud (audience) claim MUST contain the URL of the VCVerifier's M2M Token Endpoint (https://verifier.dome-marketplace-prd.org/token_m2m). The value shall be sent as a string, not as an item in an array.

- **jti**: REQUIRED. JWT ID. A unique identifier for the token, which can be used to prevent reuse of the token. Ideally, these tokens are used once. Given the very low value of the expiration time of the JWT (see below), the cache of already used `jti` claims can be held in memory, because an expired JWT MUST not be accepted even if the `jti` has not been seen before.

- **exp**: REQUIRED. Expiration time on or after which the JWT MUST NOT be accepted for processing. In a M2M flow, this JWT is used only once and the client generates the JWT immediately before using it to call the token endpoint of the VCVerifier, with no human intervention or intermediate complex processes. The expiration time MUST be set as low as possible while allowing network delays, the major component that may affect this parameter. For example, **10 seconds**, which is more than enough in most situations. In case of bad network conditions, the authentication can be retried with a new JWT. This is important for Replay protection, while simplifying management of unique `jti` claims in VC Verifier.

- **iat**: OPTIONAL. Time at which the JWT was issued.

- **verifiableCredential**: REQUIRED. This MUST contain the LEARCredential in JWT format.

The authentication token MUST be signed with the private key associated to the did:key uniquely associated to the machine/application calling the M2M Token Endpoint, which MUST be the same as the did:key inside the LEARCredential in the **verifiableCredential** claim of the authentication token.

If the VCVerifier authenticates correctly the Application, it answers with an Access Token as described in section [3.1.3.3. Successful Token Response](https://openid.net/specs/openid-connect-core-1_0.html#TokenResponse) of the OIDC specification.


