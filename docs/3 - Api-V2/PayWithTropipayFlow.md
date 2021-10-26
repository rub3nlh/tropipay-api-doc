---
tags: [Pay with Tropipay]
---
# The Pay-With-Tropipay flow

You can integrate tropipay as a payment gateway in your site. It's as easy as adding a pay-with-tropipay button in the prefered place so your customers can click it. Then, those customers (who already have their own Tropipay account with enough balance) will be redirected to a Tropipay flow to guide them through the payment process. That's it!

<!-- theme: info -->
>#### info
> Don't worry, they will be redirected back to your site once they finish the transaction. Also a callback will be sent to specified `urlNotification` URL

If you are a developer making the integration, you will probably need to check [this link about generating payment flow URL](/docs/tropipay-api-doc/b3A6MTE3MTU5ODg-getting-the-pay-with-tropipay-url) in order to generate the url to redirect the customer to.

Once clicked the button, customers will be first presented a login form so they can authenticate with their credentials. `This step is omitted if the user is already logged in to his Tropipay account`
<br>
<img src="https://raw.github.com/rub3nlh/tropipay-api-doc/master/assets/images/paga-con-tropipay-1.jpg"  width="350" style="margin: auto;">

After successful login, a small details box will be presented so they can verify purchase details
<br>
<img src="https://raw.github.com/rub3nlh/tropipay-api-doc/master/assets/images/paga-con-tropipay-2.jpg"  width="350" style="margin: auto;">


Before executing payment, an operation validation code is required so user will either receive a verification code by SMS or be required to type a 2FA verification code to validate the transaction
<br>
<img src="https://raw.github.com/rub3nlh/tropipay-api-doc/master/assets/images/paga-con-tropipay-3.jpg"  width="350" style="margin: auto;">

Once set the verification code and clicked the confirmation button, a Successful Operation page will be presented to the customer to confirm purchase and redirect her back to your site

<br>
<img src="https://raw.github.com/rub3nlh/tropipay-api-doc/master/assets/images/paga-con-tropipay-5.jpg"  width="350" style="margin: auto;">

#### Callback Signature: 

The `urlNotification` parameter allows to specify an URL to which we will send a notification callback.
All callbacks contains the parameter signature that validate it is a verified Tropipay Callback. 
You need to check the signature of any callback for security reasons:

`signature = sha256( bankOrderCode + userEmail + sha1(userPassword) + originalCurrencyAmount )
`

Below an example payload sent to your notification URL:

```json
{
  "status": "OK",
  "data": {
    "signature": "wsaer4w534hd55345y5hTDd3TSL863K3ldk3",
    "id": 27376,
    "reference": "1574194435610",
    "bankOrderCode": "1574194435610",
    "provider": 4,
    "userId": "103ec000-703b-11e9-8aea-b7e74734087c",
    "bookingDate": "2019-11-19T20:13:55.613Z",
    "days": null,
    "amount": 100000,
    "currency": "EUR",
    "originalCurrencyAmount": 100000,
    "destinationAmount": 96450,
    "destinationCurrency": "EUR",
    "conversionRate": 1,
    "depositaccountId": null,
    "state": 5,
    "serviceId": 2,
    "paymentcardId": "95aa7620-0b05-11ea-b517-dfa69b738ed5",
    "expirationDate": "2019-11-20T08:55:00.000Z",
    "movementTypeId": 2,
    "transactionId": 111111,
    "isInternal": false,
    "agent": "TROPIPAY",
    "ip": "127.0.0.1",
    "reasonId": "",
    "reasonDes": "",
    "errorReason": "",
    "notificationUrl": "",
    "riskFlag": 0,
    "riskScore": 0,
    "createdAt": "2019-11-19T20:13:55.614Z",
    "updatedAt": "2019-11-19T20:30:04.083Z",
    "paymentcard": {
      "id": "95aa7620-0b05-11ea-b517-dfa69b738ed5",
      "reference": "di3khs1p0k369tj0h",
      "concept": "concept1",
      "description": "description1",
      "amount": 100000,
      "currency": "EUR",
      "singleUse": false,
      "reasonId": 4,
      "userId": "103ec000-703b-11e9-8aea-b7e74734087c",
      "qrImage": null,
      "shortUrl": null,
      "state": 1,
      "expirationDays": 1,
      "lang": "es",
      "urlSuccess": "https://mi-negocio.com/pago-ok",
      "urlFailed": "https://mi-negocio.com/pago-ko",
      "urlNotification": "https://mi-negocio.com/notificacion-de-pago",
      "createdAt": "2019-11-19T19:48:40.194Z",
      "updatedAt": "2019-11-19T19:48:40.194Z"
    },
    "charges": [
      {
        "id": 22742,
        "orderCode": "1574194435610",
        "cardPan": "0000",
        "cardExpirationDate": "2020-01-01T00:00:00.000Z",
        "cardHolderName": "Antonio",
        "amount": 1000,
        "currency": 978,
        "userId": "103ec000-703b-11e9-8aea-b7e74734087c",
        "bookingId": 27376,
        "errorReason": "",
        "state": 3,
        "serviceId": 2,
        "cardCountry": "",
        "cardBrand": "VISA",
        "cardCategory": "BUSINESS",
        "cardType": "CREDIT",
        "clientIp": "",
        "clientName": "Antonio",
        "clientLastName": "Lopez",
        "clientAddress": "calle 280",
        "clientPhone": "5300000000",
        "clientEmail": "user@gmail.com",
        "clientTC": "true",
        "clientCountryId": 0,
        "createdAt": "2019-11-19T20:13:55.666Z",
        "updatedAt": "2019-11-19T20:30:04.066Z"
      }
    ]
  }
}
```

#### Resources for Buttons and Styles:

Bellow you can see some designs that you can use to put your Pay-With-Tropipay button.

Button Transparent White (EN).
<img src="https://raw.github.com/rub3nlh/tropipay-api-doc/master/assets/images/boton-tropipay-en.png"  width="20%" style="background: #00000033;">

Button Transparent White (ES).
<img src="https://raw.github.com/rub3nlh/tropipay-api-doc/master/assets/images/boton-tropipay-es.png"  width="20%" style="background: #00000033;">

Button  Tropipay Logo Transparent (Only Logo).
<img src="https://raw.github.com/rub3nlh/tropipay-api-doc/master/assets/images/boton-tropipay-logo.png"  width="20%" style="background: #00000033;">

Button  Tropipay Dark Blue (EN).
<img src="https://raw.github.com/rub3nlh/tropipay-api-doc/master/assets/images/boton-tropipay-recto-EN.png"  width="30%">

Button  Tropipay Dark Blue (ES).
<img src="https://raw.github.com/rub3nlh/tropipay-api-doc/master/assets/images/boton-tropipay-recto-ES.png"  width="30%" style="background: #00000033;">

Button  Tropipay Dark Blue  - Round Corner  (ES).
<img src="https://raw.github.com/rub3nlh/tropipay-api-doc/master/assets/images/boton-tropipay-redondo-EN.png"  width="30%">

Button  Tropipay Dark Blue  - Round Corner (ES).
<img src="https://raw.github.com/rub3nlh/tropipay-api-doc/master/assets/images/boton-tropipay-redondo-ES.png"  width="30%">
