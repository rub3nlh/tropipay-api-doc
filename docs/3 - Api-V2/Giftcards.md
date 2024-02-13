# Creating and selling giftcards (for Merchants)

## Introduction

Our Payments API provides merchants with the capability to offer gift cards to their customers as a part of their business operations. With this functionality, merchants can generate, manage, and track gift cards seamlessly within their systems.

<!-- theme: info -->

> #### info
>
> You must contact support in order to activate this feature for your merchant account. Login with your account and access through the live chat in the Tropipay website

## Authentication

To access the Gift Card Management functionalities, merchants need to authenticate their requests using an access token. Each request must include an access token in the header for authentication. Please refer to our API documentation for detailed instructions on authentication.

## Creating Gift Cards

Merchants can create gift cards for their customers using a simple API call. The API endpoint for creating gift cards typically includes parameters such as the gift card amount, expiration date, and any additional metadata required by the merchant. Upon successful creation, the API returns a unique gift card identifier that can be used for further management and tracking. (Consider using a database to avoid unnecesary API request) The response is among other fields , a shortUrl that the clients can use to purchase the giftcard. Just like a regular paymentcard.

### Example Request

```http
POST /api/v2/giftcards/
Content-Type: application/json
Authorization: Bearer YOUR_API_TOKEN

{
	"reference": "GIFTCARD #230212422",
	"concept": "Tarjeta de regalo Premium",
	"description": " ",
	"amount": 10000,
	"currency": "EUR",
	"lang": "es",
	"urlSuccess": "https://www.micomercio.com/sales/success",
	"urlFailed": "https://www.micomercio.com/sales/failed",
	"urlNotification": "https://www.micomercio.com/webhooks/giftcard",
	"client": {
    "name": "John",
    "lastName": "McClane",
    "address": "Ave. Guadí 232, Barcelona, Barcelona",
    "phone": "+34645553333",
    "email": "mclane@gmail.com",
    "countryId": 0,
    "termsAndConditions": "true"
  },
	"owner": {
		"name": "John",
		"lastName": "Wick",
		"phone": "666666666",
		"email": "johnwick@yahoo.com",
		"documentId": "834873487"
	},
  "paymentMethods":["EXT","TPP"],
	"withPin": true,
	"giftcardType": 1,
	"notifyByEmail": false
}
```

### Example response

```json
{
	"id": "d2673ab0-ca02-11ee-965b-a9b190553dd2",
	"saveToken": false,
	"reference": "GIFTCARD #230212422",
	"concept": "Tarjeta de regalo Premium",
	"description": " ",
	"amount": 10000,
	"currency": "EUR",
	"singleUse": true,
	"favorite": false,
	"reasonId": 22,
	"reasonDes": null,
	"expirationDays": 0,
	"userId": "629cd1d0-ab72-11ee-8e91-e982a98d522c",
	"lang": "es",
	"imageBase": null,
	"state": 1,
	"urlSuccess": "https://www.micomercio.com/sales/success",
	"urlFailed": "https://www.micomercio.com/sales/failed",
	"urlNotification": "https://www.micomercio.com/webhooks/giftcard",
	"expirationDate": null,
	"serviceDate": "2024-02-12T05:00:00.000Z",
	"hasClient": true,
	"credentialId": 2,
	"force3ds": false,
	"origin": 2,
	"paymentcardType": 3,
	"updatedAt": "2024-02-12T23:59:59.750Z",
	"createdAt": "2024-02-12T23:59:56.390Z",
	"qrImage": "data:image/png;base64,iVBORw...",
	"shortUrl": "https://tppay.me/lsjljox0",
	"paymentUrl": null,
	"rawUrlPayment": null,
	"giftcard": {
		"id": "d29f61b0-ca02-11ee-965b-a9b190553dd2",
		"code": "8BQ9-KTKH-FI4D-0QOO",
		"amount": 10000,
		"pin": "261674",
		"state": 0,
		"expirationDate": "2025-02-12T23:59:56.745Z",
		"owner": {
			"name": "John",
			"lastName": "Wick",
			"email": "johnwick@yahoo.com",
			"phone": "666666666",
			"documentId": "834873487"
		},
		"qrImage": "data:image/png;base64,iVBORw0..."
	}
}
```

When a giftcard is successfully paid, you get a callback to the notificationUrl.

## Managing Gift Cards

You can check and filter created giftcards. This is very useful for further management and tracking.
