---
tags: [Sign Up]
---

# The Sign Up process

### Introduction
Signing up through API V2 requires some steps:
1. [Checking the email address](/reference/Tropipay-API.v2.yaml/paths/~1access~1send_email_code/post) to make sure it is valid and it is not already registered
2. [Send](/reference/Tropipay-API.v2.yaml/paths/~1access~1signup/post) the new user data
3. [Send](/reference/Tropipay-API.v2.yaml/paths/~1access~1send_phone_code/post) Phone verification code 
4. [Verify the user phone number](/reference/Tropipay-API.v2.yaml/paths/~1access~1validate_phone/post) to complete account activation

This Steps should be executed in this order to complete user registration and activation. After this, users will be able to add funds to their wallet. Upon arrival of first deposit, KYC verification will be requested too. This additional verification is mandarory for use your funds.

> Info
> In order to post to this endpoints, an ApiKey authentication is required. 
> See [Authentication](/docs/Api-V2/Authentication.md) section for more details
