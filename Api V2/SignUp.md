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
