# Ingress API

## Create Ingress Invoice

```shell
curl -X POST "https://api.onramp.com/api/v1/ingress/invoice" \
-H "Authorization: Bearer AUTH_TOKEN" \
-H "Content-Type: application/json" \
-d '{"user":"user@domain.com","fiat_amount":10000,"fiat_currency":"EUR","payment_ack_url":"www.example.com/ack_webhook","user_redirect_url":"www.example.com/user/continues/here","valid_timeout":30000}'
```
### HTTP Request

`POST https://api.onramp.com/api/v1/ingress/invoice`

> Request JSON Body

```json
{
  "user": "user@domain.com",
  "fiat_amount": 10000,
  "fiat_currency": "EUR",
  "payment_ack_url": "https://callback.url/ack_webhook",
  "user_redirect_url": "https://callback.user/user/continues/here",
  "valid_timeout": 30000
}
```

> Response JSON

```json
{
  "invoice_url": "https://api.onramp.com/ingress/62b570e8-9723-4287-a5a8-e825c2ffced2"
}
```

### Request JSON Fields

Field | Type | Description
--------- | ------- | -----------
user | String | **USR**'s account
fiat_amount | Number | The amount **USR** wants to buy
fiat_currency | String | Currency. Could be EUR|USD|... 
payment_ack_url | String | URL **ON/RAMP**'s will call when process finishes 
user_redirect_url | String | URL to redirect the **USR** when process finishes successfully
valid_timeout | Number | Amount on seconds that this process could take and allow **ON/RAMP** system to cached the creation of the invoice. If the time expires the invoice should become invalid. 


### Response JSON Fields

Field | Type | Description
--------- | ------- | -----------
invoice_url | String | Internal **ON/RAMP**'s URL Invoice Reference for that **USR**


## Callback Ingress Invoice

```shell
curl -X POST "https://callback.url/ack_webhook" \
-H "Authorization: Bearer AUTH_TOKEN" \
-H "Content-Type: application/json" \
-d '{"acks":["62b570e8-9723-4287-a5a8-e825c2ffced2","fd6151e2-83f3-418c-93aa-91987a02f538","eaa703e3-b3e3-4656-adbe-b590eaf3d5de"]}'
```
### HTTP Request

`POST https://callback.url/ack_webhook`

> Request JSON Body

```json
{ "acks": [
   "62b570e8-9723-4287-a5a8-e825c2ffced2",
   "fd6151e2-83f3-418c-93aa-91987a02f538",
   "eaa703e3-b3e3-4656-adbe-b590eaf3d5de"
  ]
}
```

### Request JSON Fields

Field | Type | Description
--------- | ------- | -----------
acks | Array | **USR**'s ack invoices


