---
tags: [Login]
---
# User Login

The login process is an important step for using the API. It will allow the merchant to get a valid and temporary user token with wich, merchat's customers will be authenticated to make actions (check balance, make movements, add beneficiaries, etc)

For Security reasons, authentication is done at Tropipay's platform. This means, only Tropipay will have access to customer's credentials. For authenticating a customer, the Merchant needs to embed a specific Tropipay endpoint inside an *iframe* tag.

The first step, then, will be to get the dynamic URL of the login page. this can be found at [/access](/reference/Tropipay-API.v2.yaml/paths/~1access/post) endpoint.

This will return a dynamic URL that will load the signup form on Tropipay platform. On successfull login, you will get the user token as response. Save that token and use it to authenticate user requests

<!-- theme: info -->
>#### info
> If you want to obtain authentication token to make user actions and you are not a merchant, you can use [this endpoint](/reference/Tropipay-API.v1.yaml/paths/~1access~1login/post)
