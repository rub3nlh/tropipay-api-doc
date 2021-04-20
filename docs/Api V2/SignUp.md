---
tags: [Sign Up]
---

# The Sign Up process

### Introduction
Signing up through API V2 requires some steps:
1. [Checking the email address](reference/Tropipay-API.v2.yaml/paths/~1access~1send_email_code) to make sure it is valid and it is not already registered
2. [Send](reference/Tropipay-API.v2.yaml/paths/~1access~1signup) the new user data
3. [Send](reference/Tropipay-API.v2.yaml/paths/~1access~1send_phone_code) Phone verification code 
4. [Verify the user phone number](reference/Tropipay-API.v2.yaml/paths/~1access~1validate_phone) to complete account activation

This Steps should be executed in this order to complete user registration and activation. After this, users will be able to add funds to their wallet. Upon arrival of first deposit, KYC verification will be requested too. This additional verification is mandarory for use your funds.

> Info
> In order to post to this endpoints, an ApiKey authentication is required. See **Authentication** section below for more details

## Authentication
For authentication you need to send the Authorization header with the value: 
```
  Bearer YOUR_TOKEN
```

where YOUR_TOKEN is formed from: 
```
  Base64( API_CODE : API_KEY )
```

an example would be:
```
  Bearer MTplMXczeTRydzVlcjNvNGY0azRjOHI3dHY0NXY=
```
Where you must calculate the token by using a Base64 of your **API_CODE + " : " + API_KEY** including the colom symbol ( : )

To obtain your **API_KEY** and **API_CODE** you must contact support team.