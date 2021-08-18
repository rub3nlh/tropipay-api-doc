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

1. Create a credential in your TropiPay account if you do not have any previously, this would be from the TropiPay dashboard security section or from the API.

2. It is strongly recommended that you store your credential data preferably in environment variables so that they are not exposed from the source code.

3. Access endpoint with POST /api/v2/access/token to request your access token, specifying the Client_Id and the Client_Secret. 

4. Once the access token has been obtained, it will be able to consume the resources allowed for the credential.




