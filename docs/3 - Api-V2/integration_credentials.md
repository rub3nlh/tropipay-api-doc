# Integration with client credentials

Client Credentials is the appropriate flow when you need to integrate an external application with the TropiPay platform, it is what is known in other contexts as _backend-to-backend_ or _machine-to-machine (M2M)_ integration. The Client Credentials grant type is used by clients to obtain an access token outside of the context of a user. This is typically used by clients to access resources about themselves rather than to access a user's resources.

### How it works

One way to verify tokens you receive in your API service is to forward the token to the OAuth server to ask if it is valid. The downside of this method is that each API request sent to your server requires sending another request to the OAuth server as well, which increases the time it takes for you to respond to your client. An alternative is to use something called local validation, a strategy popularized by JSON Web Tokens (JWT). A JWT contains your claims (client data) in unencrypted, machine-readable JSON.

How is that secure? you might be wondering. JWTs contain three parts: a header, a payload, and a signature. The header and payload are simple base64 encoded strings, which can easily be decrypted and read. The signature uses an algorithm listed in the header, along with a private key, to create a hash of the header and payload. The hash can’t be recreated without the private key, but it can be verified with a public key. 
In a way, this is like a driver’s license or a passport. It’s quite difficult to forge, but it’s very easy for somebody to look at it and see your name, date of birth, and other information. You can scan the barcode, test it with a black light, or look for watermarks to help verify its validity.

While similar in concept, a valid JWT would actually be far more difficult to forge. Someone with enough skills can create a convincing driver’s license, but without the private key it could take a modern computer years to brute force a valid JWT signature. Tokens should also have an expiration. While configurable, a solid default is one hour. This means a client would need to request a new token every 60 minutes if it needs to make a new request to your API server. This is an extra layer of security in case your token is compromised. Who knows? Maybe there’s a quantum computer out there that can recreate the signature within a couple hours.

### Flow

Instead of storing and managing API keys for your clients (other servers), you can use a third-party service to manage authorization for you. The way this works is that an API client sends a request to an OAuth server asking for an API token. That token is then sent from the API client to your API service along with their request. Once you have the client’s token, you can verify its validity without needing to store any information about the client.

In a nutshell, the integration steps are:

**1 -** Create a credential (ApiKey) in your Tropipay account accessing _"Security"_ secction of the menu and clicking _"Applications and credentials"_ button. There you will see your existent ApiKeys if exist and a button to add a new one. This step can also be done with the corresponding endpoint

**2 -** Store those credentials somewhere in your environment so they can be safe and accesible by your app

**3 -** Request a new access token using the [apiKey login endpoint](/reference/Tropipay-API.v2.yaml/paths/~1access~1token/post) This access token is mandatory for accessing all other user resource endpoints

**4 -** Consume any of the API resources that ApiKey allows according to its configuration (Generate new Payment Cards, Beneficiaries, Transfer, Market Purchase, etc.)

```plain
.        +--------+                               +---------------+
         |        |--(A)-- Authorization Grant -->| Authorization |
         |        |                               |     Server    |
         |        |<-(B)----- Access Token -------|               |
         |        |                               +---------------+
         | Client |
         |        |                               +---------------+
         |        |--(C)----- Access Token ------>|    Resource   |
         |        |                               |     Server    |
         |        |<-(D)--- Protected Resource ---|               |
         +--------+                               +---------------+

```
-   **A)** Your app authenticates with the Auth0 Authorization Server using its Client ID (_like username for your app_) and Client Secret (_like password for your app_).
-   **B)** TropiPay Authorization Server validates the Client ID and Client Secret, and  responds with an Access Token.
-   **C)** Your application can use the Access Token to call an API on behalf of itself.
-   **D)** The API responds with requested data.

### An Example

This is a step-by-step example of the integration flow

**1 -** Create a credential in your TropiPay account if you do not have any previously, this would be from the TropiPay dashboard security section or from the API. For more information see [this section](/reference/Tropipay-API.v2.yaml/paths/~1credential/post)

To create a credential, just make a POST request to the _/api/v2/credential_ endpoint specifying a few parameters, as shown in the example below: 
```plain
  POST https://www.tropipay.com/api/v2/credential

  HEADER Authorization Bearer {USER-TOKEN}

  REQUEST {
      "domain": [".*gitlab.lol.com/.*", "127.0.0.1"],
      "scope": ["ALLOW_EXTERNAL_CHARGE", "BLOCKED_MONEY_OUT", "ALLOW_PAYMENT_OUT"],
      "status": 2,
      "logo_uri": "https://gitlab.lol.com/logo",
      "client_uri": "https://gitlab.lol.com",
      "policy_uri": "https://gitlab.lol.com/policy",
      "tos_uri": "https://gitlab.lol.com/tos",
      "jwks_uri": "https://gitlab.lol.com/jwks",
      "description": "My Local server for projects dev",
      "client_name": "GitLab LoL",
      "redirect_uri":  "https://gitlab.lol.com/callback",
      "redirect_uris": [
          "https://gitlab.lol.com/callback2",
          "https://gitlab.lol.com/callback3"
      ],
      "contacts": ["support@gitlab.lol.com", "help@gitlab.lol.com"]
  }

  RESPONSE {
      "client_name": "GitLab LoL",
      "client_id": "5d6fd52d1796bd41632099cb5444b7f6",
      "client_secret": "48c0dcaa3e209ec5c4c2623154ab52ab",
      "client_id_issued_at": "2021-08-19T20:10:19.199Z",
      "client_secret_expires_at": 0,
      "client_public": "1629409307441",
      "type": 1,
      "status": 2,
      "prefix": "Bearer",
      "description": "My Local server for projects dev",
      "groupId": 52,
      "ownerId": "e2931920-e402-11ea-a30d-83c978a74aaa",
      "logo_uri": "https://gitlab.lol.com/logo",
      "client_uri": "https://gitlab.lol.com",
      "tos_uri": "https://gitlab.lol.com/tos",
      "policy_uri": "https://gitlab.lol.com/policy",
      "jwks_uri": "https://gitlab.lol.com/jwks",
      "registration_client_uri": "https://www.tropipay.com/api/v2/credential/5d6fd52d1796bd41632099cb5444b7f6",
      "userinfo_encrypted_response_alg": "RSA1_5",
      "userinfo_encrypted_response_enc": "A128CBC-HS256",
      "token_endpoint_auth_method": "client_secret_basic",
      "scope": [
          "ALLOW_EXTERNAL_CHARGE",
          "BLOCKED_MONEY_OUT",
          "ALLOW_PAYMENT_OUT"
      ],
      "domain": [
          ".*gitlab.lol.com/.*",
          "127.0.0.1"
      ],
      "contacts": [
          "support@gitlab.lol.com",
          "help@gitlab.lol.com"
      ],
      "redirect_uris": [
          "https://gitlab.lol.com/callback2",
          "https://gitlab.lol.com/callback3",
          "https://gitlab.lol.com/callback"
      ],
      "grant_types": [
          "authorization_code",
          "refresh_token",
          "client_credential"
      ]
  }
```
<!-- theme: info -->
> #### Note:
>
> In this case we are indicating that with an access token generated from the credential _'my.app.cu'_, you can only run actions based on the list of permissions shown below: 
>
> -   ALLOW_EXTERNAL_CHARGE 
> -   BLOCKED_MONEY_OUT
> -   ALLOW_PAYMENT_OUT

Example developed in Node Js: 
```plain
    npm install dotenv axios
```
Index.js with source code:

```js
const axios = require('axios').default;
const token = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IAloOV2JnRPKET1m8dmb-88';

axios({
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer ' + token,
  },
  method: 'post',
  url: 'https://www.tropipay.com/api/v2/credential',
  data: {
      "domain": [".*gitlab.lol.com/.*", "127.0.0.1"],
      "scope": ["ALLOW_EXTERNAL_CHARGE", "BLOCKED_MONEY_OUT", "ALLOW_PAYMENT_OUT"],
      "status": 2,
      "logo_uri": "https://gitlab.lol.com/logo",
      "client_uri": "https://gitlab.lol.com",
      "policy_uri": "https://gitlab.lol.com/policy",
      "tos_uri": "https://gitlab.lol.com/tos",
      "jwks_uri": "https://gitlab.lol.com/jwks",
      "description": "My Local server for projects dev",
      "client_name": "GitLab LoL",
      "redirect_uri":  "https://gitlab.lol.com/callback",
      "redirect_uris": [
          "https://gitlab.lol.com/callback2",
          "https://gitlab.lol.com/callback3"
      ],
      "contacts": ["support@gitlab.lol.com", "help@gitlab.lol.com"]
  }
})
.then(res => console.log(res.data))
.catch(error => console.log(error));
```

Run command:
```
node index.js
```
Response:
```json
{
    "client_name": "GitLab LoL",
    "client_id": "5d6fd52d1796bd41632099cb5444b7f6",
    "client_secret": "48c0dcaa3e209ec5c4c2623154ab52ab",
    "client_id_issued_at": "2021-08-19T20:10:19.199Z",
    "client_secret_expires_at": 0,
    "client_public": "1629409307441",
    "type": 1,
    "status": 2,
    "prefix": "Bearer",
    "description": "My Local server for projects dev",
    "groupId": 52,
    "ownerId": "e2931920-e402-11ea-a30d-83c978a74aaa",
    "logo_uri": "https://gitlab.lol.com/logo",
    "client_uri": "https://gitlab.lol.com",
    "tos_uri": "https://gitlab.lol.com/tos",
    "policy_uri": "https://gitlab.lol.com/policy",
    "jwks_uri": "https://gitlab.lol.com/jwks",
    "registration_client_uri": "https://www.tropipay.com/api/v2/credential/5d6fd52d1796bd41632099cb5444b7f6",
    "userinfo_encrypted_response_alg": "RSA1_5",
    "userinfo_encrypted_response_enc": "A128CBC-HS256",
    "token_endpoint_auth_method": "client_secret_basic",
    "scope": [
        "ALLOW_EXTERNAL_CHARGE",
        "BLOCKED_MONEY_OUT",
        "ALLOW_PAYMENT_OUT"
    ],
    "domain": [
        ".*gitlab.lol.com/.*",
        "127.0.0.1"
    ],
    "contacts": [
        "support@gitlab.lol.com",
        "help@gitlab.lol.com"
    ],
    "redirect_uris": [
        "https://gitlab.lol.com/callback2",
        "https://gitlab.lol.com/callback3",
        "https://gitlab.lol.com/callback"
    ],
    "grant_types": [
        "authorization_code",
        "refresh_token",
        "client_credential"
    ]
}
```
<!-- theme: info -->
> #### Note:
>
> Notice how the response returns an object with the **username** and **password** properties equivalent to **Client_Id** and **Client_Secret** respectively.

**2 -** It is strongly recommended that you store your credential data preferably in environment variables so that they are not exposed from the source code.

    # Environment File <==> .env
    TROPIPAY_CLIENT_ID={response.data.username}
    TROPIPAY_CLIENT_SECRET={response.data.password}

**3 -** Access endpoint with POST _/api/v2/access/token_ to request your access token, specifying the Client_Id and the Client_Secret. For more information see [this section](/reference/Tropipay-API.v2.yaml/paths/~1access~1token/post) 

Index.js with source code:

```js
const axios = require('axios').default;
axios({
  method: 'post',
  url: 'https://www.tropipay.com/api/v2/access/token',
  data: {
    "grant_type":"client_credentials",
    "client_id": "991aea6a4587040942b8599a6d8fbebb",
    "client_secret": "ec51a20c4a8db60693e3ffae7b32222b"
  }
})
.then(res => console.log(res.data))
.catch(error => console.log(error));
```

Run command:

    node index.js

Response:

```json
{
  access_token: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjcmVkZW50aWFsTmFtZSI6ImdpdGxhYi5teSIsImlkIjoiZTI5MzE5MjAtZTQwMi0xMWVhLWEzMGQtODNjOTc4YTc0YWFhIiwiaWF0IjoxNjI5MzExMDM5LCJleHAiOjE2MjkzOTc0Mzl9.u2Ir3y2ADUZAscN051zbc7bLk7FtbvYzyb34s6R3voY",
  refresh_token: "Z2l0bGFiLm15OmVhYjIwNTVkYWM0ODQyOTcwNWQ4YmEzYTRiMDRmZDBl",
  token_type: "Bearer",
  expires_in: 1629397439,
  scope: "ALLOW_EXTERNAL_CHARGE BLOCKED_MONEY_OUT"
}
```

**4 -** Once the access token has been obtained, it will be able to consume the resources allowed for the credential. For example let's see how to consume  [credential/grant/list](/reference/Tropipay-API.v2.yaml/paths/~1credential~1grant~1list/post) using your new access token, this service returns the list of available permissions: 

Index.js with source code:

```js
const token = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjcmVkZW50aWFsTmFtZSI6ImdpdGxhYi5teSIsImlkIjoiZTI5MzE5MjAtZTQwMi0xMWVhLWEzMGQtODNjOTc4YTc0YWFhIiwiaWF0IjoxNjI5MzExMDM5LCJleHAiOjE2MjkzOTc0Mzl9.u2Ir3y2ADUZAscN051zbc7bLk7FtbvYzyb34s6R3voY';

axios({
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer ' + token,
  },
  method: 'get',
  url: 'https://www.tropipay.com/api/v2/credential/grant/list'
})
.then(res => console.log(res.data))
.catch(error => console.log(error));
```

Run command:

    node index.js

Response:
```json
[
  { name: "ALLOW_EXTERNAL_CHARGE", label: "payments" },
  { name: "BLOCKED_MONEY_OUT", label: "payments" },
  { name: "ALLOW_OTA_CHARGE", label: "payments_in" },
  { name: "ALLOW_SELF_CHARGE", label: "payments_in" },
  { name: "ALLOW_REDEEM_CODE", label: "payments_in" },
  { name: "ALLOW_CHARGE_VOUCHER", label: "payments_in" },
  { name: "ALLOW_EXTERNAL_TOPUP", label: "payments_in" },
  { name: "ALLOW_INTERNAL_TRANSFER_IN", label: "payments_in" },
  { name: "ALLOW_GIFT_CARD", label: "payments_out" },
  { name: "ALLOW_RECHARGE", label: "payments_out" },
  { name: "ALLOW_EXTERNAL_TRANSFER", label: "payments_out" },
  { name: "ALLOW_INTERNAL_TRANSFER_OUT", label: "payments_out" },
  { name: "ALLOW_CREATE_BENEFICIARY", label: "app" },
  { name: "ALLOW_UPDATE_BENEFICIARY", label: "app" },
  { name: "ALLOW_DELETE_BENEFICIARY", label: "app" },
  { name: "ALLOW_UPDATE_PROFILE", label: "app" },
  { name: "ALLOW_PAYMENT_IN", label: "generic" },
  { name: "ALLOW_PAYMENT_OUT", label: "generic" },
  { name: "ALLOW_MARKET_PURCHASES", label: "generic" },
  { name: "UPLOAD_DOCUMENT", label: "support" }
]
```
