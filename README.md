# 1. Feasibility API
### Hämta alla levererbara platser

***Request:***

```http
GET /api/1.0/feasibility HTTP/1.1
```

***Response:***

```http
HTTP/1.1 200 OK
Last-Modified: Sun, 10 Jan 2016 12:03:28 GMT
Content-Type: application/json
```
```javascript
[
	{
		"address": {
			"street": "Testvägen",
			"number": "100",
			"littera": "",
			"postalCode": "10000",
			"city": "Ankeborg"
		},
		"building": {
			"distinguisher": "", // "Set if something is needed to distinguish a specific building on the address."
			"type": "MDU" // "'MDU' = apartment building, 'SDU' = villa"
		},
		"realEstate": {
			"label": "PENGABINGEN 1",
			"municipality": "ANKEBORG"
		},
		"coordinate": {
			"latitude": 6581619.085,
			"longitude": 1628539.32,
			"projection": "WGS84"
		},
		"district": "GAMLA STAN",
		"suppliers": [ // "Optional; should only be provided if not heavy too calculate."
			{
				"name": "STOKAB",
				"fiberStatus": "IN_REAL_ESTATE", // "'AT_ADDRESS', 'AT_SITE_BOUNDARY'"
				"statusValidationRequired": true // "Indicates if the fiberStatus needs manual validation to assure availability."
			}
		],
 		"relatedPointIds": [
			"CDE678",
			"CDE901"
		]
	},
	...
]
```


# 2. Price Estimate API
### Hämta prisuppskattning på en förbindelse

***Request:***

```http
POST /api/1.0/priceEstimates HTTP/1.1
Content-Type: application/json
```
```javascript
{
	"fromAddress": {  /* May be set to null if any product only requires one point (address). E.g. Star. */ 
		"city" : "Stockholm",
		"street": "Drottninggatan",
		"number": "52",
		"littera": "A"
	},
	"toAddress": { 
		"city" : "Stockholm",
		"street": "Drottninggatan",
		"number": "68",
		"littera": "A"
	},
	"redundancy": {
		"type": "Full", /* "'Normal', 'Full'" */
		"toPointId": "" /* May be set to null */
	},
	"customerType": "Commercial", /* "e.g. 'Commercial', 'Residential'" */
	"serviceLevel": "Premium", /* "e.g. 'Base', 'Gold', 'Premium'" */
	"productTypes": [
		"All" /* "e.g. 'All', 'Point2Point', 'Star'" */
	],
	"parameters": [ /* "Will not be implemented by Innofactor" */
		{
			"name": "ConnectorType",
			"value": "SC/APC"
		},
		...
	],
	"contractPeriodMonths": 12, /* "1, 3 or 5 years (in months)." */
	"noOfFibers": 1 // "number of wanted fiber pairs (or single fibers depending on product /* "Allan: input tack." */
}
```

***Response:***

```http
HTTP/1.1 200 OK
Content-Type: application/json
```
```javascript
[
	{
		"supplier": "STOKAB",
		"products": [
			{
				"productId" : "8u3-3563-3635-365-ff",
				"name": "Point2Point",
				"status": "AVAILABLE",
				"comment": "",
				"items": [ /* 'Items' is ambiguous and should be replaced with something more descriptive */
					{
						"name": "distance",
						"value": "987"
					},
					...
				],
				"price": {
					"oneTimeFee": 15100.0,
					"monthlyFee": 1200.0,
					"items": [ 
						{
							"name": "Connection based on distance",
							"parameters": [
								{
									"name": "distance",
									"value": "987"
								},
								...
							],
							"oneTimeFee": 5000.0,
							"monthlyFee": 1000.0
						},
						{
							"name": "Service level Premium",
							"parameters": [...],
							"oneTimeFee": 0.0,
							"monthlyFee": 200.0
						},
						{
							"name": "Connector",
							"parameters": [...],
							"oneTimeFee": 100.0,
							"monthlyFee": 0.0
						},
						{
							"name": "ResidentialNetwork",
							"parameters": [
								{
									"name": "NoOfRooms",
									"value": "10"
								}
							],
							"oneTimeFee": 10000.0,
							"monthlyFee": 0.0
						},
						...
					]
				},
				"subProducts": [ /* "Will be related to the product" */
					{
						"productId": "08y3gt4-g-gt54-i",
						"name": "a suiting name",
						"price": {
							"oneTimeFee": 1100.0,
							"monthlyFee": 100.0
						}
					},
					...
				]
			},
			{
				"productId": "87sg-098dsaf-098sudg-098gf",
				"actualAddress" : {},
				"name": "Star",
				"status": "POSSIBLE_WITH_CONDITIONS", // "'NOT_AVAILABLE', 'AVAILABLE'"
				"comment": "Connection to anslutningsnod is necessary for address to be available",
				"items": [],
				"price": null,
				"subProducts": []
			},
			{
				"productId": "87sg-098dsaf-098sudg-098gf",
				"actualAddress" : {
				...
				},
				"name": "Star",
				"status": "POSSIBLE_WITH_CONDITIONS", // "'NOT_AVAILABLE', 'AVAILABLE'"
				"comment": "Connection to anslutningsnod is necessary for address to be available",
				"items": [],
				"price": null,
				"subProducts": []
			},
			...
		],
	},
	...
]
```

# 3. Offer Inquiry API
### Skicka en offert-förfrågan för en förbindelse av en specifik produkt från en specifik leverantör

***Request:***

```http
POST /api/1.0/offerInquiry HTTP/1.1
Content-Type: application/json
```
```javascript
{
	"supplier": "STOKAB",
	"productId": "0d13c5e0-ce23-41a0-87b5-f480479fa71e", 
	"productType": "Star",
	"referenceId": "CH-12345", /* "client own reference for this inquiry, could be empty" */
	"fromAddress": {
		"city" : "Stockholm",
		"streetName": "Drottninggatan",
		"streetNumber": "52",
		"streetLittera": "A"
	},
	"toAddress": {
		"city" : "Stockholm",
		"streetName": "Drottninggatan",
		"streetNumber": "68",
		"streetLittera": "A"
	},
	"redundancy": {
		"type": "Full", /* "'Normal', 'Full'" */
		"toPointId": "CBA123"
	},
	"customerType": "Commercial", /* "e.g. 'Commercial', 'Residential'" */
	"serviceLevel": "Premium", /* "e.g. 'Base', 'Gold', 'Premium'" */
	"parameters": [  /* "Will not be implemented by Innofactor" */
		{
			"name": "ConnectorType",
			"value": "SC/APC"
		},
		...
	],
	"subProducts": [
		"bb634e19-07c3-4d33-9dd8-8ee27dfa2f29", 
		"12a63f10-24a9-4530-979c-964db4cfa9b8"
	],
	"contractPeriodMonths": 12,
	"noOfFibers": 1, /* "Number of wanted fiber pairs (or single fibers depending on product)" */
	"asyncAnswerAllowed": true /* "If asychronous answer is ok (might result in an extra charge if manual)" */
}
```

***Response:***

```http
HTTP/1.1 201 CREATED
Last-Modified: Mon, 11 Jan 2015 12:03:28 GMT
Location: /api/1.0/inquiries/ec4bc754-6a30-11e2-a585-4fc569183061
Content-Type: application/json
```
```javascript
{
	"inquiryId": "I982283", /* Incident-number in CRM. */
	"referenceId": "CH-12345",
	"status": {
		"state": "WAIT_ASYNC_ANSWER", /* "'DONE_SUCCESS', 'DONE_FAILED', 'DONE_ASYNC_ANSWER_SUCCESS', 'DONE_ASYNC_ANSWER_FAILED'" */
		"message": "",
		"createDateTime": "2016-08-21T08:01:06.000Z",
		"doneDateTime": "2016-08-22T10:15:01.000Z", /* "Should be null if not yet done." */
	},
	"supplier": "STOKAB",
	"offerValidUntilDate": "2016-01-31",
	"connectionId": "", /* "May be set to the identifier for the connection if that is already generated when inquiry is answered." */
	"deliveryDurationDays": 20, /* "Days from order to delivered connection." */
	"product": {
		"productId": "bd16d8d5-c147-4490-8b7a-93193fce8fb3",
		"name": "Point2Point",
		"status": "AVAILABLE",
		"comment": "",
		"items": [
			{
				"name": "distance",
				"value": "987"
			},
			...
		],
		"subProducts": [
		{
			"productId": "5f3e0024-77aa-42df-9faf-2088d328975c",
			"name": "awesomeProduct",
			"price": {
				"oneTimeFee": "1287.99",
				"monthlyFee": "283.00"
			}
		}...
		],
		"price": {
			"status": "ESTIMATED", /* "'ESTIMATED', 'OFFER'. Estimated price can be delivered in synchronous answer and then be overridden by an offer in an asynchronous answer" */
			"oneTimeFee": 15100.0,
			"monthlyFee": 1200.0,
			"items": [ /* "Will return empty array for now" */
				{
					"name": "Connection based on distance",
					"parameters": [
						{
							"name": "distance",
							"value": "987"
						},
						...
					],
					"oneTimeFee": 5000.0,
					"monthlyFee": 1000.0
				},
				{
					"name": "Service level Premium",
					"parameters": [...],
					"oneTimeFee": 0.0,
					"monthlyFee": 200.0
				},
				{
					"name": "Connector",
					"parameters": [...],
					"oneTimeFee": 100.0,
					"monthlyFee": 0.0
				},
				{
					"name": "ResidentialNetwork",
					"parameters": [
						{
							"name": "NoOfRooms",
							"value": "10"
						}
					],
					"oneTimeFee": 10000.0,
					"monthlyFee": 0.0
				},
				...
			]
			
		}
	}
}
```

### Hämta uppdatering på en skickad offert-förfrågan

***Request:***

```http
GET /api/1.0/inquiries/ec4bc754-6a30-11e2-a585-4fc569183061 HTTP/1.1
```

***Response:***

```http
HTTP/1.1 200 OK
Last-Modified: Mon, 11 Jan 2015 12:03:28 GMT
Content-Type: application/json
```
```javascript
{
	"inquiryId": "ec4bc754-6a30-11e2-a585-4fc569183061",
	"referenceId": "CH-12345",
	"status": {
		"state": "DONE_ASYNC_ANSWER_SUCCESS",
		"message": "",
		"createDateTime": "2016-08-21T08:01:06.000Z",
		"doneDateTime": "2016-08-22T10:15:01.000Z", /* "Should be null if not yet done." */
	},
	"supplier": "STOKAB",
	"offerValidUntilDate": "2016-01-31",
	"connectionId": "",
	"deliveryDurationDays": 20,
	"product": {
		"productId": "141c78ce-acfa-4f77-aebc-1ab94327872a",
		"name": "Point2Point",
		"status": "AVAILABLE",
		"comment": "",
		"items": [ 
			{
				"name": "distance",
				"value": "1046"
			},
			...
		],
		"subProducts": [
		{
			"productId": "141c78ce-acfa-4f77-aebc-1ab94327872a",
			"name": "awesomeProduct",
			"price": {
				"oneTimeFee": "1287.99",
				"monthlyFee": "283.00"
			}
		}...
		],
		"price": {
			"status": "OFFER",
			"oneTimeFee": 15100.0,
			"monthlyFee": 1500.0,
			"items": [ 
				{
					"name": "Connection based on distance",
					"parameters": [
						{
							"name": "distance",
							"value": "1046"
						},
						...
					],
					"oneTimeFee": 5000.0,
					"monthlyFee": 1300.0
				},
				{
					"name": "Service level Premium",
					"parameters": [...],
					"oneTimeFee": 0.0,
					"monthlyFee": 200.0
				},
				{
					"name": "Connector",
					"parameters": [...],
					"oneTimeFee": 100.0,
					"monthlyFee": 0.0
				},
				{
					"name": "ResidentialNetwork",
					"parameters": [
						{
							"name": "NoOfRooms",
							"value": "10"
						}
					],
					"oneTimeFee": 10000.0,
					"monthlyFee": 0.0
				},
				...
			]
		}
	}
}
```

### Hämta slutförda förfrågningar som krävde asynkront svar

Anropet skall returnera samtliga förfrågningar som skett sedan since. Om en förfrågan har exakt angiven tidpunkt som doneDateTime så skall den ordern inte ingå i svaret.

Listan är oföränderlig. Vid två tidpunkter, t1 och t2, där t2 inträffar efter t1, skall tjänstens svar vid t2 alltid omfatta hela t1. Svaret kan alltså enbart växa, inte förändras.

Av denna anledningen är det viktigt att tjänstens svar inte är en projektion på förfrågningar, som kan ändra status, utan endast innefattar sådana förfrågningar som har nått en slutstatus.

Listan över förfråningar skall vara stigande i kronologisk ordning (senaste förfrågan som har förändrats sist). Klienten kan då använda det sista lästa förfrågan som since-parameter i nästföljande anrop.

Enbart förfrågningar som har status DONE_ASYNC_ANSWER_SUCCESS eller DONE_ASYNC_ANSWER_FAILED får förekomma i svaret.

Om inte since skickas med så skall alla slutförda förfrågningar returneras.

För att hämta detaljerad information om respektive förfrågan, använd Hämta uppdatering på en skickad förfrågan.

***Request:***

```http
GET /api/1.0/inquiries?since=2016-08-22T15:01:02.000Z
```

***Response:***

```http
HTTP/1.1 200 OK
Content-Type: application/json
```
```javascript
[
	{
		"inquiryId": "ec4bc754-6a30-11e2-a585-4fc569183061",
		"state": "DONE_ASYNC_ANSWER_SUCCESS",
		"message": ""
	},
	{
		"inquiryId": "fc4bc754-6a30-11e2-a585-4fc569183432",
		"state": "DONE_ASYNC_ANSWER_FAILED",
		"message": "An error occured"
	},
	...
]
```

# 4. Order API
### Lägg en beställning på en tidigare mottagen offert och komplettera med kund och fakturainformation.

***Request:***

```http
POST /api/1.0/orders HTTP/1.1
Content-Type: application/json
```
```javascript
{
	"inquiryId": "ec4bc754-6a30-11e2-a585-4fc569183061",
	"responsiblePerson": {
		"firstName": "Anders",
		"lastName": "Larsson",
		"phoneNumber": "46701234567",
		"email": "anders.larsson@comhem.com"
	},
	"endCustomer": {
		"companyName" : "HSB",
		"firstName": "Sven",
		"lastName": "Svensson",
		"phoneNumber": "46702233445",
		"email": "sven.svensson@company.com"
	},
	"invoiceGroup": "IK-12345"
}
```

***Response:***

```http
HTTP/1.1 201 CREATED
Last-Modified: Mon, 11 Jan 2015 12:05:28 GMT
Location: /api/1.0/orders/fc6cd754-6a30-11e2-a585-4fc569185689
Content-Type: application/json
```
```javascript
{
	"orderId": "fc6cd754-6a30-11e2-a585-4fc569185689",
	"inquiryId": "ec4bc754-6a30-11e2-a585-4fc569183061",
	"supplier": "STOKAB",
	"product": "Point2Point",
	"status": {
		"state": "ORDERED",
		"message": "",
		"orderDateTime": "2015-01-11T11:34:00.000Z"
	}
}
```

### Hämta uppdatering på en lagd order

***Request:***

```http
GET /api/1.0/orders/fc6cd754-6a30-11e2-a585-4fc569185689 HTTP/1.1
```

***Response:***

```http
HTTP/1.1 200 OK
Last-Modified: Tue, 15 Feb 2015 15:12:53 GMT
Content-Type: application/json
```
```javascript
{
	"orderId": "fc6cd754-6a30-11e2-a585-4fc569185689",
	"inquiryId": "ec4bc754-6a30-11e2-a585-4fc569183061",
	"supplier": "STOKAB",
	"product": "Point2Point",
	"status": {
		"state": "DELIVERED", /* "ORDERED", "DELIVERED", "REJECTED" */
		"message": "",
		"orderDateTime": "2015-01-11T11:34:00.000Z",
		"doneDateTime": "2015-02-15T14:49:12.000Z"
	},
	"responsiblePerson": {
		"firstName": "Anders",
		"lastName": "Larsson",
		"phoneNumber": "46701234567",
		"email": "anders.larsson@comhem.com"
	},
	"endCustomer": {
		"companyName": "HSB",
		"firstName": "Sven",
		"lastName": "Svensson",
		"phoneNumber": "46702233445",
		"email": "sven.svensson@company.com"
	},
	"invoiceGroup": "IK-12345"
}
```

### Hämta slutförda ordrar

Anropet skall returnera samtliga ordrar som skett sedan since. Om en order har exakt angiven tidpunkt som doneDateTime så skall den ordern inte ingå i svaret.

Listan är oföränderlig. Vid två tidpunkter, t1 och t2, där t2 inträffar efter t1, skall tjänstens svar vid t2 alltid omfatta hela t1. Svaret kan alltså enbart växa, inte förändras.

Av denna anledningen är det viktigt att tjänstens svar inte är en projektion på ordrar, som kan ändra status, utan endast innefattar sådana ordrar som har nått en slutstatus.

Listan över ordrar skall vara stigande i kronologisk ordning (senaste order som har förändrats sist). Klienten kan då använda det sista lästa orderns  som since-parameter i nästföljande anrop.

Enbart ordrar som har status DELIVERED eller REJECTED får förekomma i svaret.

Om inte since skickas med så skall alla slutförda ordrar returneras.

För att hämta detaljerad information om respektive order, använd Hämta uppdatering på en lagd order.

***Request:***

```http
GET /api/1.0/orders?since=2016-08-22T10:09:23.000Z
```

***Response:***

```http
HTTP/1.1 200 OK
Content-Type: application/json
```
```javascript
[
	{
		"orderId": "fc6cd754-6a30-11e2-a585-4fc569185689",
		"state": "DELIVERED",
		"message": ""
	},
	{
		"orderId": "ab6cd754-6a30-11e2-a585-4fc569185643",
		"state": "REJECTED",
		"message": "The order couldn't be delivered because of no available fibers"
	},
	...
]
```
# 5. Invoice Group
### Hämta alla fakturamottagare för specifik kund

***Request:***

```http
GET /api/1.0/invoiceGroup HTTP/1.1
```

***Response:***

```http
HTTP/1.1 200 OK
Content-Type: application/json
```
```javascript
[
	{
		"name": "ComHem",
		"invoiceGroup": "9834",
		"clientNumber": "23892382938"
	},
	...
]
