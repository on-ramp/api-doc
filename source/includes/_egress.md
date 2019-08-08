# Egress API

## Create Egress Invoice

> Example Call

```shell
curl -X POST "https://api.onramp.com/v1/egress/invoice" \
-H "Authorization: Bearer AUTH_TOKEN" \
-H "Content-Type: application/json" \
-d '{"user":"user@domain.com","fiat_amount":10000,"fiat_currency":"EUR","payment_ack_url":"www.example.com/ack_webhook","user_redirect_url":"www.example.com/user/continues/here","valid_timeout":30000}'
```

> Request JSON Body

```json
{
  "user": "user@domain.com",
  "fiat_amount": 10000,
  "fiat_currency": "EUR",
  "payment_ack_url": "https://callback.url/ack_webhook?param1=value1&param2=value2",
  "user_redirect_url": "https://callback.user/user/continues/here",
  "valid_timeout": 30000
}
```

> Response JSON

```json
{
   "invoice_id": "62b570e8-9723-4287-a5a8-e825c2ffced2", 
   "invoice_url": "https://api.onramp.com/egress/62b570e8-9723-4287-a5a8-e825c2ffced2"
}
```

### HTTP Request

<aside class="success"><b><code>POST /v1/egress/invoice</code></b></aside>


### Request JSON Fields

Field | Type | Description
--------- | ------- | -----------
user | String | **USR**'s account
fiat_amount | Number | The amount **USR** wants to buy
fiat_currency | String | Currency. Could be EUR|USD|... 
payment_ack_url | String | URL **ON/RAMP**'s will call when process finishes. This URL can contain any number of query parameters whose **ON/RAMP** is going to maintain in the [Callback Process](#callback-egress-invoice).
user_redirect_url | String | URL to redirect the **USR** when process finishes successfully
valid_timeout | Number | Amount on seconds that this process could take and allow **ON/RAMP** system to cached the creation of the invoice. If the time expires the invoice should become invalid. 


### Response JSON Fields

Field | Type | Description
--------- | ------- | -----------
invoice_id | String | Internal **ON/RAMP**'s Invoide Identifier
invoice_url | String | Internal **ON/RAMP**'s URL Invoice Reference for that **USR**

## Callback Egress Invoice

> Example Callback 

```shell
curl "https://callback.url/ack_webhook?param1=value1&param2=value2&user=user@domain.com&invoice_id=62b570e8-9723-4287-a5a8-e825c2ffced2&issued_at=1565184896401"
```

> Response should be 200, otherwise system will retry with exponential decay 5 times


Callback Egress Invoice, is the callback **ON/RAMP** system is going to call back to the **URL** set in [Create Egress Invoice](#create-egress-invoice) endpoint on the field `payment_ack_url`, once the Egress has been successfully completed. This callback will be done with exactly the same **URL** setup in `payment_ack_url`. This means that if `payment_ack_url** contains Query Parameters in the URL it will be maintain.

Additionally with your `payment_ack_url`, **ON/RAMP** will add some **Query Parameters** that will overwrite original `payment_ack_url` query parameters if they are the same. This **ON/RAMP** query parameters are like *reserved word* parameters keys with information about the invoice which is being callback from.

**URL** in **cURL** tab is an example and is the same put it as an example in field ``payment_ack_url` on [Create Egress Invoice](#create-egress-invoice) section.


### Reserved Query Parameters

Parameter | Type | Description
--------- | ------- | -----------
user | String | **USR**'s account sent in [Create Egress Invoice](#create-egress-invoice) endpoint
invoice_id | String | **USR**'s invoice id returned in [Create Egress Invoice](#create-egress-invoice) endpoint
issued_at | Number | Invoice completion Timestamp

## Query Egress Invoice

> Example Call

```shell
curl "https://api.onramp.com/v1/egress/invoice/62b570e8-9723-4287-a5a8-e825c2ffced2" \
-H "Authorization: Bearer AUTH_TOKEN" 
```

> Response JSON

```json
{
  "invoice_id": "62b570e8-9723-4287-a5a8-e825c2ffced2",
  "invoice_url": "https://api.onramp.com/egress/62b570e8-9723-4287-a5a8-e825c2ffced2",
  "status": "processing"
}
```

### HTTP Request

<aside class="success"><b><code>GET /v1/egress/invoice/:id</code></b></aside>

### Query Parameter Path

Field | Type | Description
--------- | ------- | -----------
id | String | **USR**'s invoice id returned in [Create Egress Invoice](#create-egress-invoice) call

### Response JSON Fields

Field | Type | Description
--------- | ------- | -----------
invoice_id | String | Internal **ON/RAMP**'s Invoide Identifier
invoice_url | String | Internal **ON/RAMP**'s URL Invoice Reference for that **USR**
status | String | Status of current Invoice. Can be: <code>invalid&#124;processing&#124;rejected&#124;complete</code>

