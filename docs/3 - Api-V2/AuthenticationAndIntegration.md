## Integration

El API de TropiPay da soporte de integracion basado en el estandar OAuth 2.

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

## OAuth Grant Types

The OAuth framework specifies several grant types for different use cases, as well as a framework for creating new grant types.

The most common OAuth grant types used in TropiPay are listed below:
- **Client Credentials:** for External App to TropiPay integration. 
- **Authorization Code with PKCE:** for External App to TropiPay User integration.
- **Refresh Token**

## Client Credentials

One way to verify tokens you receive to your API service is to forward the token to the OAuth server to ask if it is valid. The downside to this method is each API request sent to your server requires a request sent to the OAuth server as well, which increases the time it takes for you to respond to your client. An alternative is to use something called local validation, a strategy popularized by JSON Web Tokens (JWT). A JWT contains your claims (client data) in unencrypted, machine-readable JSON.

When using the local validation pattern to validate an API token (JWT), you can use math to validate that:

The token your API is receiving hasn’t been tampered with The token your API is receiving hasn’t expired That certain pieces of JSON data encoded in the token are what you expect them to be

How is that secure? you might be wondering. JWTs contain three parts: a header, a payload, and a signature. The header and payload are simple base64 encoded strings, which can easily be decrypted and read. The signature uses an algorithm listed in the header, along with a private key, to create a hash of the header and payload. The hash can’t be recreated without the private key, but it can be verified with a public key.

In a way, this is like a driver’s license or a passport. It’s quite difficult to forge, but it’s very easy for somebody to look at it and see your name, date of birth, and other information. You can scan the barcode, test it with a black light, or look for watermarks to help verify its validity.

While similar in concept, a valid JWT would actually be far more difficult to forge. Someone with enough skill can create a convincing driver’s license, but without the private key it could take a modern computer years to brute force a valid JWT signature. Tokens should also have an expiration. While configurable, a solid default is one hour. This means a client would need to request a new token every 60 minutes if it needs to make a new request to your API server. This is an extra layer of security in case your token is compromised. Who knows? Maybe there’s a quantum computer out there that can recreate the signature within a couple hours.

Now that you understand the basics of the OAuth 2.0 client credentials flow works, let’s build a Node API that uses Client Credentials and Okta.