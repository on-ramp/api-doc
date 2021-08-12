# Server to Server (S2S)

## Steps

1. Merchant makes a request to `create_operation` endpoint.
2. Merchant gets `op_id` in the response and stores it for further usage if needed.
3. At that moment, we start calling `callback_url` with the different possible status.
4. When merchant receives `WAITING_USER_TO_BE_REDIRECTED`, they redirect the user to the 3DS url.
5. The user is redirected first to a url of our server in order for us to update the status of the operation and then it gets to the actual 3DS page.
6. User completes 3DS (with success or fail) and gets redirected to our webhook.
7. Our webhook updates the operation and redirects the user to the url provided on `user_redirect_url`.
8. After that we continue calling `callback_url` whenever we have an update on the status. Alternatively, merchant could call operation_status.
9. Once the merchant receives either `ACCEPTED` or `REJECTED`, the process finishes.

## Authentication

> Header Based

```json
"Authorization": "Bearer <API_KEY>"
```

Each API Key is associated to a specific MID.

Operations created under a specific API key will only be visible with the same API key.

## Status definition

Here is the list of all the existing status and their definition.

-   `PENDING`:
    Operation has only been created and it is waiting for a response to get a 3DS url or an error.
-   `WAITING_USER_TO_BE_REDIRECTED`:
    3DS url has been created and user should be redirected to that.
-   `USER_REDIRECTED`:
    User has been redirected to the 3DS page.
-   `PROCESSING_USER_PAYMENT`:
    User has completed 3DS and the payment is being processed.
-   `ACCEPTED`:
    The payment has been accepted.
-   `REJECTED`:
    The payment has been rejected by the bank.
-   `CANCELLED`:
    Operation has been cancelled.

Diagram for visual explanation of the flow:

![](./images/s2s_flow.png)

## Callbacks

Whenever there's a change on a operation status, a status-change callback will be triggered.

Status-change callbacks will be retried with exponential decay until they get correctly answered.

It will be a POST to the callback_url with the following payload in json format:

**WARNING**: in rare cases, it might be possible for status-change callbacks to be duplicated or received out of order, therefore we recommend to check the `date` field to detect this anomalities.

### HTTP Callback Request

<aside class="success"><b><code> POST callback_url</code></b></aside>

`callback_url`: URL sent as value in `create_operation` endpoint

> Example Call

```shell
curl https://CALLBACK_URL                                    \
  -H "x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000"                   \
  -H "Content-Type: application/json"                                                     \
  -X POST -d '{
		"op_id": "c6cce9a4-af6d-4572-a0ab-d7bc2ad159ba",
			"status": {
				"waiting_user_to_be_redirected": {
					"url": "https://api.onramp.ltd/rpc/deposit"
				}
			},
			"date": 1626971748
		}'
```

> Request Json Example

```json
{
	"op_id": "c6cce9a4-af6d-4572-a0ab-d7bc2ad159ba",
		"status": {
			"waiting_user_to_be_redirected": {
				"url": "https://api.onramp.ltd/rpc/deposit"
			}
		},
		"date": 1626971748
}
```

### Request Callback JSON Fields

Field  | Description                                     | Type      
------ | ----------------------------------------------- | --------- 
op_id  | To which operation this status change refers to | uuid      
status | The new status the operation is currently at    | Status    
date   | Timestamp of the creation of the new status     | timestamp 

### Status Field

 Field | Description | Type
-------------------------------|-------------------------------------------------|--------
 pending | initial state | Object
 waiting_user_to_be_redirected | Merchant shall redirect the user to the given url. | RedirectObj
 user_redirected | Waiting user to complete 3DS step. | Object
 processing_user_payment | Waiting acquirer answer. | Object
 accepted | Operation completed. Final state | Object
 rejected | Operation was cancel on aquarier side. Final state | ErrorObj
 cancelled | Operation was cancel on our side. Final state | CancelObj

### RedirectObj 

 Field | Description | Type
-------------------------------|-------------------------------------------------|-------
 url | where to redirect the user to. | url

### ErrorObj
 Field | Description | Type
-------------------------------|----------------------------------------------|-------
 error_code | original specific aquarier error identifier. | string
 error_reason | Enum of common error reasons. | ErrorType

### ErrorType

- `'ISSUER_OFFLINE'`.
- `'UNSUPPORTED_CARD_COUNTRY'`.
- `'INSUFICIENT_FUNDS'`.
- `'UNSUPPORTED_CARD_TYPE'`.
- `'UNSUPPORTED_CURRENCY'`.
- `'INVALID_CARDNUMBER'`.
- `'CVV2_FAILURE'`.
- `'UNSPECIFIED_CC_ERR'`.

### CancelObj

 Field | Description | Type
-------------------------------|-------------------------------------------------|-------
 reason | Description of the merchant or user action after which the operation was cancel. | string


### Merchant Response 

### HTTP Status 200

### Response Json Fields

Field  | Description                       | Type   
------ | --------------------------------- | ------ 
op_id  | ID of the operation to be updated | uuid   
action | Action to be performed            | Action 

### Action

Field | Description | Type 
-------- | -------- | -------- 
request_attempt_cancel | We'll try to cancel the operation, but it should not assumed it will happen. | object 
continue | Merchant acknowledges it has received the callback. | object 

> Response Json Example

```json
{
    "action": {
        "request_attempt_cancel": {}
    }
}
```

> _Note: If an unknown status is sent, merchant should respond with `continue`_

```json
{
    "action": {
        "continue": {}
    }
}
```

### Card fields

This table defines all the potential required fields for credit card as well as some required only sometimes. To see which one applies to your MID, check the `credit_card_fields` endpoint.

| Field                  | Description                                                                                                               | Type   | Always Required |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------- | ------ | --------------- |
| card_number            | Valid card number                                                                                                         | string | true            |
| card_expiry_month      | The expiry month on the credit card. With the format of MM.                                                               | string | true            |
| card_expiry_year       | The expiry year on the credit card. With the format of YY.                                                                | string | true            |
| card_cvv               | The CVV value for the card                                                                                                | string | true            |
| card_holder_first_name | The card first holder name as it appears on the card                                                                      | string | false           |
| card_holder_last_name  | The card last holder name as it appears on the card                                                                       | string | false           |
| email                  | Email of the user making the payment                                                                                      | string | false           |
| street                 | Street of the user's billing address                                                                                      | string | false           |
| city                   | City of the user's billing address                                                                                        | string | false           |
| postal_code            | Postal code of the user's billing address                                                                                 | string | false           |
| state                  | State of the user's billing address                                                                                       | string | false           |
| country                | Country of the user's billing address. It should be [ISO 3166 complaint](https://www.iso.org/iso-3166-country-codes.html) | string | false           |


## Get credit card fields

<aside class="success"><b><code>GET /rpc/credit_card_fields</code></b></aside>

Get the list of required credit-card fields for the current API Key. Notice different API keys might required a different set of fields to be provided.

> Example Call

```shell
curl https://BASE_URL/rpc/credit_card_fields                                    \
  -H "x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000"                   \
  -H "Content-Type: application/json"                                                     \
```

### HTTP Request

- No Body

### HTTP Response

- Status 200

### HTTP Response Fields

Field  | Description                          | Type                                         
------ | ------------------------------------ | -------------------------------------------- 
fields | List of available credit card fields | CreditCardField -> FieldDescrtiption

### CreditCardField

One of:

-   `'credit_card_number'`
-   `'credit_card_name'`
-   ... see previous [_Card fields_](#card-fields) section.

[See credit card fields section](#card-fields)

### FieldDescription

Field | Description | Type |
-------- | -------- | -------- |
description | Description about the field | string |
regex | Regular expression to validate the field | string |
optional | Boolean flag to know if the field is optional | boolean |
examples | List of example values | array |


> Request Json Example

```json
{
    "fields": {
        "credit_card_number": {
            "description": "The credit card number",
            "regex": "^(?:4[0-9]{12}(?:[0-9]{3})?|[25][1-7][0-9]{14}|6(?:011|5[0-9][0-9])[0-9]{12}|3[47][0-9]{13}|3(?:0[0-5]|[68][0-9])[0-9]{11}|(?:2131|1800|35d{3})d{11})$",
            "optional": false,
            "examples": ["4242424242424242", "3700 0000 0000 002"]
        },
        "credit_card_expiry_month": {
            "description": "The month when the credit card expires",
            "regex": "^0[1-9]|1[0-2]$",
            "optional": false,
            "examples": ["01", "12"]
        }
    }
}
```

## Create Operation

<aside class="success"><b><code> POST /rpc/create_operation</code></b></aside>

This endpoint is meant to be used to start a payment once all the user the details for the operation has been gathered from the user (amount, currency, credit card details, ...).

**\*To be noted**: if you receive a specific error about incorrect credit card details (code `ERR_CODE_INVALID_CARD_DETAILS`), please first make a call to `credit_card_fields` to get the new valid ones and repeat the operaiton with those.\*


> Example Call

```shell
curl https://BASE_URL/rpc/create_operation                                    \
  -H "x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000"                   \
  -H "Content-Type: application/json"                                                     \
	-X POST -d '{
    "fiat_amount": "1000",
    "fiat_currency": "EUR",
    "callback_url": "https://api.betway.com/deposit-webhook",
    "user_redirect_url": "https://betway.api/user/deposits",
    "card_details": {
        "card_number": "4242424242424242",
        "card_expiry_month": "12",
        "card_expiry_year": "21",
        "card_cvv": "356"
    }
}'
```

### HTTP Request Fields

| Field             | Description                                                                       | Type              |
| ----------------- | --------------------------------------------------------------------------------- | ----------------- |
| fiat_amount       | Integer between a min value and a max value, in cents.                            | string            |
| fiat_currency     | ISO 4127 currency 3 digits code. https://www.iso.org/iso-4217-currency-codes.html | string            |
| callback_url      | URL to be called by us to inform about updates on the operation status.           | string            |
| user_redirect_url | URL to use to redirect the user after 3DS on the operation status.                | string            |
| card_details      | Object of credit card details                                                     | Credit-Card-Field |

> Request Json Example

```json
{
    "fiat_amount": "1000",
    "fiat_currency": "EUR",
    "callback_url": "https://api.betway.com/deposit-webhook",
    "user_redirect_url": "https://betway.api/user/deposits",
    "card_details": {
        "card_number": "4242424242424242",
        "card_expiry_month": "12",
        "card_expiry_year": "21",
        "card_cvv": "356"
    }
}
```

### HTTP Response

- Status 200

### HTTP Response Fields

Field | Description                                    | Type            
----- | ---------------------------------------------- | --------------- 
op_id | ID of the operation to be used as a reference. | Array[uuid][1] 

> Response Json Example

```json
[{ "op_id": "7ffb7577-e4f9-4980-89d0-a1936f9088f4" }]
```

## Operation status

<aside class="success"><b><code>GET /rpc/operation_status?op_id={op_id}</code></b></aside>

This endpoint can be used both to know about the status of an operation or as an alternative to callbacks. At any moment, if you would like to know about the status of an operation, you can make a request here to know about it.

It returns the list of status-changes related to the given event.


> Example Call

```shell
curl https://BASE_URL/rpc/operation_status?op_id=7ffb7577-e4f9-4980-89d0-a1936f9088f4     \
  -H "x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000"                   \
  -H "Content-Type: application/json"                                                     \
}'
```

### HTTP Request Fields

- No body

### HTTP Response

- Status 200

### HTTP Response Fields

- [See callback status section](#callbacks)


## Update Operation

<aside class="success"><b><code> POST /rpc/update_operation</code></b></aside>

Send an action to a given operation, this action will be executed as if it were responsed on a status-change event callback.

> Example Call

```shell
curl https://BASE_URL/rpc/update_operation                                    \
  -H "x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000"                   \
  -H "Content-Type: application/json"                                                     \
	-X POST -d '{
    "op_id": "5b99dfb2-ef07-4fb7-9d20-62d63ce0f737",
    "action": {
        "request_attempt_cancel": {}
    }
}'
```

### HTTP Request Fields

Field  | Description                                                                                                 | Type  
------ | ----------------------------------------------------------------------------------------------------------- | ------
op_id  | ID of the operation to be updated.                                                                          | uuid  
action | Action to be performed. Please see, [callbacks section](https://hackmd.io/m7zCvFtkSNGdrsKNYcTu4A#Callbacks) | Action

> Request Json Example

```json
{
    "op_id": "5b99dfb2-ef07-4fb7-9d20-62d63ce0f737",
    "action": {
        "request_attempt_cancel": {}
    }
}
```


### HTTP Response

- Status 200

### HTTP Response 

- Empty Json

> Response Json Example

```json
{}
```

## Testing

### Unstable environment testing:

While testing on this environment set `CVV` to:

-   `001` to immediate accept the CC payment.

-   `002` for 3DS.

-   `003` to immediate reject the CC payment.

## API changes & backward compatibility:

To keep flexibility for future new features, while not loosing backward compatibility, merchants using this API are requested to:

-   Assume that any Json could contain extra fields not specified on this document.

-   Not to assume that objects with an unespecified contain will be either empty or not.

-   Be able to answer unkown types of status change callbacks by answering `continue`.
