# Authorization Code with PKCE
The Authorization Code Grant Type is probably the most common of the OAuth 2.0 grant types that you’ll encounter. It is used by both web apps and native apps to get an access token after a user authorizes an app. 

### How it works

The authorization code grant is used when an application exchanges an authorization code for an access token. After the user returns to the application via the redirect URL, the application will get the authorization code from the URL and use it to request an access token. This request will be made to the token endpoint.

The Authorization Code flow is best used in web and mobile apps. Since the Authorization Code grant has the extra step of exchanging the authorization code for the access token, it provides an additional layer of security not present in the Implicit grant type.

If you’re using the Authorization Code flow in a mobile app, or any other type of application that can’t store a client secret, then you should also use the PKCE extension, which provides protections against other attacks where the authorization code may be intercepted.

The code exchange step ensures that an attacker isn’t able to intercept the access token, since the access token is always sent via a secure backchannel between the application and the OAuth server.

### Flow

The Authorization Code grant type is used by web and mobile apps. It differs from most of the other grant types by first requiring the app launch a browser to begin the flow. At a high level, the flow has the following steps:

```plain                                                             
.                                                +-------------------+
                                                 |   Auth  Server    |
       +--------+                                | +---------------+ |
       |        |--(A)- Authorization Request ---->|               | |
       |        |       + t(code_verifier), t_m  | | Authorization | |
       |        |                                | |    Endpoint   | |
       |        |<-(B)---- Authorization Code -----|               | |
       |        |                                | +---------------+ |
       | Client |                                |                   |
       |        |                                | +---------------+ |
       |        |--(C)-- Access Token Request ---->|               | |
       |        |          + code_verifier       | |    Token      | |
       |        |                                | |   Endpoint    | |
       |        |<-(D)------ Access Token ---------|               | |
       +--------+                                | +---------------+ |
                                                 +-------------------+
```

- The application opens a browser to send the user to the OAuth server
- The user sees the authorization prompt and approves the app’s request
- The user is redirected back to the application with an authorization code in the query string
- The application exchanges the authorization code for an access token


### 1 - Get the User’s Permission
OAuth is all about enabling users to grant limited access to applications. The application first needs to decide which permissions it is requesting, then send the user to a browser to get their permission. To begin the authorization flow, the application constructs a URL like the following and opens a browser to that URL.

```plain
https://tropipay-dev.herokuapp.com/api/v2/access/authorize
      ?response_type=code
      &client_id=1b125cefa4e6aa5fc044a06190953eac
      &redirect_uri=https//my-app.com/auth/callback
      &scope=ALLOW_GET_BALANCE
      &state=xcoiv98y2kd22vusuye3kch
```

Here’s each query parameter explained:

- **response_type=code**: This tells the authorization server that the application is initiating the authorization code flow.
- **client_id**: The public identifier for the application, obtained when the developer first registered the application- .
- **redirect_uri**: Tells the authorization server where to send the user back to after they approve the request- .
- **scope**: One or more space-separated strings indicating which permissions the application is requesting. The specific OAuth API you’re using will define the scopes that it supports.
- **state**: The application generates a random string and includes it in the request. It should then check that the same value is returned after the user authorizes the app. This is used to prevent CSRF attacks.

When the user visits this URL, the authorization server will present them with a prompt asking if they would like to authorize this application’s request.

### 2 - Redirect Back to the Application

If the user approves the request, the authorization server will redirect the browser back to the redirect_uri specified by the application, adding a code and state to the query string. For example, the user will be redirected back to a URL such as:

```plain
https//my-app.com/auth/callback?code=g0ZGZmNjVmOWIjNTk2NTk4ZTYyZGI3&state=xcoiv98y2kd22vusuye3kch
```

The state value will be the same value that the application initially set in the request. The application is expected to check that the state in the redirect matches the state it originally set. This protects against CSRF and other related attacks.

The code is the authorization code generated by the authorization server. This code is relatively short-lived, typically lasting between 1 to 10 minutes depending on the OAuth service.

### 3 - Exchange the Authorization Code for an Access Token
We’re about ready to wrap up the flow. Now that the application has the authorization code, it can use that to get an access token. The application makes a POST request to the service’s token endpoint with the following parameters:

- **grant_type=authorization_code**: This tells the token endpoint that the application is using the Authorization Code grant type.
- **code**: The application includes the authorization code it was given in the redirect.
- **redirect_uri**: The same redirect URI that was used when requesting the code. Some APIs don’t require this parameter, so you’ll need to double check the documentation of the particular API you’re accessing.
- **client_id**: The application’s client ID.
- **client_secret**: The application’s client secret. This ensures that the request to get the access token is made only from the application, and not from a potential attacker that may have intercepted the authorization code.

The token endpoint will verify all the parameters in the request, ensuring the code hasn’t expired and that the client ID and secret match. If everything checks out, it will generate an access token and return it in the response!

```plain
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
  "access_token":"MTQ0NjJkZmQ5OTM2NDE1ZTZjNGZmZjI3",
  "token_type":"bearer",
  "expires_in":3600,
  "refresh_token":"IwOGYzYTlmM2YxOTQ5MGE3YmNmMDFkNTVk",
  "scope":"ALLOW_GET_BALANCE"
}
```

The Authorization Code flow is complete! The application now has an access token it can use when making API requests.

### 4 - PKCE extension

The Proof Key for Code Exchange (PKCE) extension describes a technique for public clients to mitigate the threat of having the authorization code intercepted. The technique involves the client first creating a secret, and then using that secret again when exchanging the authorization code for an access token. 

- **code verifier:** A cryptographically random string that is used to correlate the authorization request to the token request.

- **code challenge:** A challenge derived from the code verifier that is sent in the authorization request, to be verified against later.

- **code challenge method:** A method that was used to derive code challenge.

**Base64url:** Encoding Base64 encoding using the URL, with all trailing '=' characters omitted and without the inclusion of any line breaks, whitespace, or other additional characters.

This way if the code is intercepted, it will not be useful since the token request relies on the initial secret. The full spec is available as [RFC7636](https://tools.ietf.org/html/rfc7636).

### An example
Now that you understand the basics of the OAuth 2.0 authorization code flow works, let’s create a complete example that contemplates the entire flow. 

> #### Note:
>
> You can see the full demo at this [link](https://github.com/tropipay/demo-oauth-code-nodejs)

#### Requirements
It is necessary to create a credential app previously, see the sequence of screenshots shown below.
![credential.png](https://stoplight.io/api/v1/projects/cHJqOjE1ODAz/images/2V3MYoYGZC0)

#### Configurations 

It is necessary to use two endpoints from the TropiPay authorization server, in order to facilitate the understanding of the subject, these urls are stored in variables that will be used later.
```js
const url_tropipay = "https://sandbox.tropipay.me";
const oauth_authorize = url_tropipay + '/api/v2/access/authorize';
const oauth_token = url_tropipay + '/api/v2/access/token';
```

It is also important to specify the application credentials that will be used, as described in the previous section, these credentials can be obtained from your TropiPay account by accessing:

```plain
Credentials (Menu > Seguridad > APP y Credenciales)
```

These credentials are stored in constants vars but we recommend using environment variables or another mechanism to avoid security issues
```js.
const client_id = "946cef5ecad81f282e20d9bbb712ec64";
const client_secret = "e25bbb41a2a2ed365e685e0edbb81162";
```

When you try to authenticate this way, you need to specify a url that expects the authorization code, it also specifies what are the necessary permissions to execute the operation
```js
const redirect_uri = "http://localhost:5000/oauth/response";
const scope = "ALLOW_GET_BALANCE";
```

Security is important, in this case it is optional but it is recommended to send the **state** option and check it in the url that the authorization code expects
```js
const state = "abcd-1234";
```

Similar to the previous one it is recommended to use the verification by PKCE.
```js 
const code_verifier = "1234-abcd-1234";
const code_challenge = "N2_wPQ7X9iP5bKXcw05rqHw1S7OwFuU4Nqi6ccr_LEs";
const code_challenge_method = "S256";
```

#### Step 1
The first step is to request the authorization code. the best way is to redirect from your server to the url stored in the oauth_authorize variable

```js
const param = qs.stringify({
    response_type: "code",
    client_id,
    client_secret,
    redirect_uri,
    code_challenge,
    code_challenge_method,
    state,
    scope
});
res.redirect(oauth_authorize + "?" + param);
```
#### Step 2
Once the user completes the authentication and authorization process, you must check that everything is in order and request the access token

```js
if (req.query['state'] !== state) {
    console.log('NOT secure, the state value not match');
}
//... confifure options for get authorization code
const param = {
    grant_type: "authorization_code",
    code: req.query['code'],
    client_id,
    client_secret,
    redirect_uri,
    code_verifier,
    scope
};
//... save authorization code
const token = await axios.post(oauth_token, param);
access_token = token.data.access_token;
```
#### Get user resource
If everything has gone well then with the value of the access token the user resource is requested
```js
let [balanceData, profileData] = await Promise.all([
    axios({
        headers: {'Authorization': 'Bearer ' + access_token},
        url: url_tropipay + "/api/users/balance"
    }),
    axios({
        headers: {'Authorization': 'Bearer ' + access_token},
        url: url_tropipay + "/api/users/profile"
    }),
]);
res.end(`<p> Hi <strong>${profileData.data.name} </strong> this is your 
          TPP balance: <strong>${balanceData.data.balance / 100} </strong> EUR </p>`);
```

