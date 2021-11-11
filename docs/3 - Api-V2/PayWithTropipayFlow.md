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
    "signature": "0a421aea27cbc7254c291419d016e3692f02e836d32fdfd9ed3d1ee6ed660dfe"
    "id": 375558,
    "reference": "some reference",
    "bankOrderCode": "TX1636652414219",
    "provider": 4,
    "userId": "a268e700-e0bb-123a-8b91-bd37f597bc41",
    "bookingDate": null,
    "days": null,
    "amount": 1000,
    "currency": "EUR",
    "originalCurrencyAmount": "1000",
    "destinationAmount": 1000,
    "destinationCurrency": "EUR",
    "conversionRate": null,
    "depositaccountId": null,
    "state": 2,
    "serviceId": null,
    "paymentcardId": null,
    "expirationDate": null,
    "movementTypeId": 5,
    "transactionId": 124198,
    "isInternal": true,
    "agent": "TROPIPAY",
    "ip": "127.0.0.1",
    "reasonId": 6,
    "reasonDes": null,
    "errorReason": null,
    "notificationUrl": "https://your.site/tropipay-handler",
    "riskFlag": 0,
    "riskScore": 0,
    "createdAt": "2021-11-11T17:40:14.221Z",
    "updatedAt": "2021-11-11T19:45:17.614Z"
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
