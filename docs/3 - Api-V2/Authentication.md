---
tags: [Authentication]
---

# Authentication

To access the different endpoints, you need to use an Authorization Header type `Bearer`. There are two different authentication methods depending on the action to execute. Think about it like two group of endpoints:
- Private Label merchant configurations and action endpoints
- User actions' endpoints

<!-- theme: info -->
>#### info
> If you are making an app integration to access your own account and you want to obtain an authentication token to make user actions, you can use [this endpoint](/reference/Tropipay-API.v2.yaml/paths/~1access~1token/post)

## Private Label Merchant Actions

For these endpoints's authentication you need to send the Authorization header with the value: 
```
  Bearer MERCHANT_TOKEN
```

where MERCHANT_TOKEN is formed from: 
```
  Base64( API_CODE : API_KEY )
```

an example Authorization header would be:
```
  Authorization: Bearer MTplMXczeTRydzVlcjNvNGY0azRjOHI3dHY0NXY=
```
Where you must build the token by using a Base64 of your **API_CODE + " : " + API_KEY** including the colom symbol ( : )


<!-- theme: info -->
>#### info
> Don't forget the spaces around the colon symbol

To obtain your **API_KEY** and **API_CODE** you must contact support team.

## User Actions

For these endpoints, you will need to use the user token received after successfull login of the user.

Get the value and add the corresponding header to all subsequent requests. The header value must be in the form

```
  Bearer USER_TOKEN
```
an example header would be:
```
  Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImUyOTMxOTIwLWU0MDItMTFlYS1hMzBkLTgzYzk3OGE3NGFhYSIsImVtYWlsIjoidG9ueWtzc2FAZ21haWwuY29tIiwiaWF0IjoxNjE2NzAxMDY2LCJleHAiOjE2MTY3ODc0NjZ9.1JCk_lYwBLlUxPC-UHrSUrpHecDJZeW-1H_cH13T37w
```

<!-- theme: info -->
>#### info
> Remember that user token expires in time


