## Integration

The TropiPay API supports integration based on the OAuth 2 standard. 

## Protocol Flow

```
     +--------+                               +---------------+
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
- **Resource Owner:** Entity that can grant access to a protected resource. Typically, this is the end-user.
- **Resource Server:** Server hosting the protected resources. This is the API you want to access.
- **Client:** Application requesting access to a protected resource on behalf of the Resource Owner.
- **Authorization Server:** Server that authenticates the Resource Owner and issues access tokens after getting proper authorization. In this case, Auth0.

## OAuth Grant Types

The OAuth framework specifies several grant types for different use cases, as well as a framework for creating new grant types. The most common OAuth grant types used in TropiPay are listed below:
- **1. Client Credentials:** for External App to TropiPay integration. 
- **2. Authorization Code with PKCE:** for External App to TropiPay User integration.
- **3. Refresh Token**

## 1. Client Credentials

Client Credentials is the appropriate flow when you need to integrate an external application with the TropiPay platform, it is what is known in other contexts as *backend-to-backend* or *machine-to-machine (M2M)* integration. The Client Credentials grant type is used by clients to obtain an access token outside of the context of a user. This is typically used by clients to access resources about themselves rather than to access a user's resources.

### 1.1 Introduction
One way to verify tokens you receive to your API service is to forward the token to the OAuth server to ask if it is valid. The downside to this method is each API request sent to your server requires a request sent to the OAuth server as well, which increases the time it takes for you to respond to your client. An alternative is to use something called local validation, a strategy popularized by JSON Web Tokens (JWT). A JWT contains your claims (client data) in unencrypted, machine-readable JSON.

When using the local validation pattern to validate an API token (JWT), you can use math to validate that:

The token your API is receiving hasn’t been tampered with The token your API is receiving hasn’t expired That certain pieces of JSON data encoded in the token are what you expect them to be

How is that secure? you might be wondering. JWTs contain three parts: a header, a payload, and a signature. The header and payload are simple base64 encoded strings, which can easily be decrypted and read. The signature uses an algorithm listed in the header, along with a private key, to create a hash of the header and payload. The hash can’t be recreated without the private key, but it can be verified with a public key.

In a way, this is like a driver’s license or a passport. It’s quite difficult to forge, but it’s very easy for somebody to look at it and see your name, date of birth, and other information. You can scan the barcode, test it with a black light, or look for watermarks to help verify its validity.

While similar in concept, a valid JWT would actually be far more difficult to forge. Someone with enough skill can create a convincing driver’s license, but without the private key it could take a modern computer years to brute force a valid JWT signature. Tokens should also have an expiration. While configurable, a solid default is one hour. This means a client would need to request a new token every 60 minutes if it needs to make a new request to your API server. This is an extra layer of security in case your token is compromised. Who knows? Maybe there’s a quantum computer out there that can recreate the signature within a couple hours.

### 1.2 Flow
Instead of storing and managing API keys for your clients (other servers), you can use a third-party service to manage authorization for you. The way this works is that an API client sends a request to an OAuth server asking for an API token. That token is then sent from the API client to your API service along with their request. Once you have the client’s token, you can verify its validity without needing to store any information about the client.

```
     +--------+                               +---------------+
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

- **A)** Your app authenticates with the Auth0 Authorization Server using its Client ID (*like username for your app*) and Client Secret (*like password for your app*).
- **B)** TropiPay Authorization Server validates the Client ID and Client Secret, and  responds with an Access Token.
- **C)** Your application can use the Access Token to call an API on behalf of itself.
- **D)** The API responds with requested data.

### 1.3 Example
Now that you understand the basics of the OAuth 2.0 client credentials flow works, let’s let's create a complete example that contemplates the entire flow.

**1.3.1.** Create a credential in your TropiPay account if you do not have any previously, this would be from the TropiPay dashboard security section or from the API. For more information see [this section](/reference/Tropipay-API.v2.yaml/paths/~1credential/post)

To create a credential, just make a POST request to the */api/v2/credential* endpoint specifying a few parameters, as shown in the example below: 
```
POST https://www.tropipay.com/api/v2/credential

HEADER Authorization Bearer {USER-TOKEN}

REQUEST {
    "name": "my.app.cu",
    "domain": ".*tropipay.com.* www.my.app.cu *.localhost.*",
    "scope": "ALLOW_EXTERNAL_CHARGE ALLOW_OTA_CHARGE"
}

RESPONSE {
    "status": "OK",
    "data": {
        "name": "my.app.cu",
        "domain": ".*tropipay.com.* www.my.app.cu *.localhost.*",
        "ownerId": "e2931920-e402-11ea-a30d-83c978a74aaa",
        "prefix": "Bearer",
        "refresh": "1d",
        "type": 1,
        "status": 0,
        "username": "991aea6a4587040942b8599a6d8fbebb",
        "password": "ec51a20c4a8db60693e3ffae7b32222b",
        "public": "1629304998681",
        "groupId": 63,
        "updatedAt": "2021-08-18T16:43:18.876Z",
        "createdAt": "2021-08-18T16:43:18.876Z",
        "expiration": null,
        "redirect": null
    }
}
```

Example developed in Node Js: 
```
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
		"name": "my.app.cu",
		"domain": ".*tropipay.com.* www.my.app.cu *.localhost.*",
		"scope": "ALLOW_EXTERNAL_CHARGE BLOCKED_MONEY_OUT ALLOW_OTA_CHARGE"
	}
})
.then(res => console.log(res.data))
.catch(error => console.log(error));
```

Run command:
```
node index.js
```

Resonse:
```
{
    "status": "OK",
    "data": {
        "name": "my.app.cu",
        "domain": ".*tropipay.com.* www.my.app.cu *.localhost.*",
        "ownerId": "e2931920-e402-11ea-a30d-83c978a74aaa",
        "prefix": "Bearer",
        "refresh": "1d",
        "type": 1,
        "status": 0,
        "username": "991aea6a4587040942b8599a6d8fbebb",
        "password": "ec51a20c4a8db60693e3ffae7b32222b",
        "public": "1629304998681",
        "groupId": 63,
        "updatedAt": "2021-08-18T16:43:18.876Z",
        "createdAt": "2021-08-18T16:43:18.876Z",
        "expiration": null,
        "redirect": null
    }
}
```
<!-- theme: info -->
>#### info
> Notice how the response returns an object with the **username** and **password** properties equivalent to **Client_Id** and **Client_Secret** respectively.

2. It is strongly recommended that you store your credential data preferably in environment variables so that they are not exposed from the source code.

```
# Environment File <==> .env
TROPIPAY_CLIENT_ID={response.data.username}
TROPIPAY_CLIENT_SECRET={response.data.password}
```

3. Access endpoint with POST */api/v2/access/token* to request your access token, specifying the Client_Id and the Client_Secret. For more information see [this section](/reference/Tropipay-API.v2.yaml/paths/~1access~1token/post) 

Index.js with source code:
```js
const axios = require('axios').default;
axios({
	method: 'post',
	url: 'https://www.tropipay.com/api/v2/access/token',
	data: {
		"grant_type":"client_credentials",
		"client_id": "991aea6a4587040942b8599a6d8fbebb",
		"client_secret": "796c0519dace8efdeaf59e6ad40e811a"
	}
})
.then(res => console.log(res.data))
.catch(error => console.log(error));
```

Run command:
```
node index.js
```
 
Resonse:
```json
{
  access_token: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjcmVkZW50aWFsTmFtZSI6ImdpdGxhYi5teSIsImlkIjoiZTI5MzE5MjAtZTQwMi0xMWVhLWEzMGQtODNjOTc4YTc0YWFhIiwiaWF0IjoxNjI5MzExMDM5LCJleHAiOjE2MjkzOTc0Mzl9.u2Ir3y2ADUZAscN051zbc7bLk7FtbvYzyb34s6R3voY",
  refresh_token: "Z2l0bGFiLm15OmVhYjIwNTVkYWM0ODQyOTcwNWQ4YmEzYTRiMDRmZDBl",
  token_type: "Bearer",
  expires_in: 1629397439,
  scope: "ALLOW_EXTERNAL_CHARGE BLOCKED_MONEY_OUT"
}
```
4. Once the access token has been obtained, it will be able to consume the resources allowed for the credential.




