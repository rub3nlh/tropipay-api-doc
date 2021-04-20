---
tags: [Authentication]
---

# Authentication

To access the different endpoints, you need to use an Authorization Header type `Bearer`. There are two different authentication methods depending on the action to execute. Think about it like two group of endpoints:
- Merchant configurations and actions endpoints
- User actions endpoints

## Merchant Actions

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

<!-- theme: info -->
>#### info
> Remember that user token expires in time


