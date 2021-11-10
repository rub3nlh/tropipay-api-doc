---
tags: [Integration]
---

## Integration

The TropiPay API supports integration based on the OAuth 2 standard. 

## Protocol Flow

```plain
.        +--------+                               +---------------+
         |        |--(A)- Authorization Request ->|   Resource    |
         |        |                               |     Owner     |
         |        |<-(B)-- Authorization Grant ---|               |
         |        |                               +---------------+
         |        |
         |        |                               +---------------+
         |        |--(C)-- Authorization Grant -->| Authorization |
         | Client |                               |     Server    |
         |        |<-(D)----- Access Token -------|               |
         |        |                               +---------------+
         |        |
         |        |                               +---------------+
         |        |--(E)----- Access Token ------>|    Resource   |
         |        |                               |     Server    |
         |        |<-(F)--- Protected Resource ---|               |
         +--------+                               +---------------+

```
Figure 1: Abstract Protocol Flow

An OAuth 2.0 flow has the following roles:

-   **Resource Owner:** Entity that can grant access to a protected resource. Typically, this is the end-user.
-   **Resource Server:** Server hosting the protected resources. This is the API you want to access.
-   **Client:** Application requesting access to a protected resource on behalf of the Resource Owner.
-   **Authorization Server:** Server that authenticates the Resource Owner and issues access tokens after getting proper authorization. In this case, Auth0.

## OAuth Grant Types

The OAuth framework specifies several grant types for different use cases, as well as a framework for creating new grant types. The most common OAuth grant types used in TropiPay are listed below:

-   **1. [Client Credentials](./integration_credentials.md):** for External App to TropiPay integration. 
-   **2. [Authorization Code with PKCE](./authorization_code.md):** for External App to TropiPay User integration.
-   **3. [Refresh Token](./refresh_token.md)**




