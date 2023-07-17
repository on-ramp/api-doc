# Crypto Ingress Flow

This documentation contains the main endpoints that participate in the Crypto Ingress Flow. Most of them are called directly from the **iframe**
integration that you can see [here](#crypto-iframe-integration).

From the User point of view, there are mainly 3 steps in the flow:

1. User requests a new Crypto Address (_BTC or BCH_) to transfer coins into.
2. Once we verify that the user has transferred coins to that address, we notify back to the Merchant that there are new funds available from this user.
3. On this notification, the Merchant should request to initiate a new Invoice as in other Payments Methods.

Steps **2 and 3** should be interactively between Merchant and ON/RAMP because of the nature of Blockchain transactions, since we need to wait to the user to transfer some coins but since the process is completely asynchronous and distributed, we dont know when the user is going to do the transfer and how much is going to transfer.

## User request a Crypto Address

As it is mentioned in [Crypto Iframe Integration](#crypto-iframe-integration), this is done by calling the endpoint in the **iframe** `get_address`. This is forwarding the call to the public endpoint:

<aside class="success"><b><code>POST /blockchain/api/v1/{chain}/address/new</code></b></aside>

> Example cURL

```shell
curl -X POST -H "Content-Type: application/json" -H "x-xco-authorization: Bearer MERCHANT_API_KEY" -d '{"merchant_customer_id": "123","fiat_amount": 10000, "fiat_currency": "EUR", "notify_new_funds_url": "http://mydomain.com/callback/notify/crypto_done" }' http://localhost:7000/blockchain/api/v1/btc/address/new
```

> ReqAddress

```json
{
  "merchant_customer_id": "merchant_customer_id",
  "fiat_amount": 10000,
  "fiat_currency": "EUR",
  "notify_new_funds_url": "http://mydomain.com/callback/notify/crypto_done"
}
```

> AddressResponse

```json
{
  "address": "ADDRESS_BASE58_OR_SPECIFIC_BLOCKCHAIN_ENCODING",
  "chain": "btc",
  "qr_base64": "BASE_64_IMAGE_OF_ADDRESS"
}
```

> ErrorResponse (Status 400)

```json
{
  "error_code": "ERROR_ADDRESS_ALREADY_IN_USE",
  "in_use_addr": "ADDRESS"
}
```

```json
{
  "error_code": "ERROR_MERCHANT_NOT_CONFIGURED_YET",
  "merchant_id": "MERCHANT_API_KEY"
}
```

> ErrorResponse (Status 401)

```json
{
  "error_code": "ERROR_MERCHANT_WRONG_API_KEY",
  "api_key": "MERCHANT_API_KEY"
}
```

> ErrorResponse (Status 500)

```json
{
  "error_code": "ERROR_CANNOT_CREATE_QR_CODE",
  "message": "Some message"
}
```

```json
{
  "error_code": "ERROR_UNKNOWN_ERROR",
  "message": "Some message"
}
```

### Authentication

| type   | Header Key            | Header Value              |
| ------ | --------------------- | ------------------------- |
| Header | `x-xco-authorization` | `Bearer merchant_api_key` |

### Parameters

| name  | type | values     | description                              |
| ----- | ---- | ---------- | ---------------------------------------- |
| chain | enum | `btc, bch` | Blockchain the user requested ingress to |

### Body

| Content Type       | Data Type    |
| ------------------ | ------------ |
| `application/json` | `ReqAddress` |

### Responses

| http code | content-type       | response          |
| --------- | ------------------ | ----------------- |
| `200`     | `application/json` | `AddressResponse` |
| `400`     | `application/json` | `ErrorResponse`   |
| `401`     | `application/json` | `ErrorResponse`   |
| `500`     | `application/json` | `ErrorResponse`   |

## ON/RAMP notify Merchant on new Crypto Funds

As we have seen in the previous endpoint the Merchant is going to provide some URL to notify back when there is some new funds from some user.

> **_IMPORTANT_**: This is not the same as `payment_ack_url` because up to this point we don't know how much money the user is going to transfer and if in fact the user is going to transfer or not. This SHOULD NOT be consider as an INVOICE.

When we are ready to do the callback notifying about new Crypto funds we are going to do the following call:

<aside class="success"><b><code>POST `notify_new_funds_url`</code></b></aside>

> Example cURL

```shell
curl -X POST 'http://mydomain.com/callback/notify/crypto_done' \
-H 'Content-Type: application/json' \
-H 'x-xco-authorization: Bearer MERCHANT_API_KEY' \
-d '{
    "crypto_tx_ref": "123",
    "high_risk": false
}'
```

> Request

```json
{
  "crypto_tx_ref": "REFERENCE_ID_TO_BE_USED_IN_CALLING_CREATE_CRYPTO_INGRESS_INVOICE",
  "high_risk": false
}
```

### Authentication

| type   | Header Key            | Header Value              |
| ------ | --------------------- | ------------------------- |
| Header | `x-xco-authorization` | `Bearer merchant_api_key` |

### Responses

| http code | content-type       | response |
| --------- | ------------------ | -------- |
| `200`     | `application/json` | If Ok    |
| `500`     | `application/json` | If Error |

## Merchant Create new Crypto Invoice

Once the Merchant receives the notification on new crypto funds it should call the following endpoint to initiate the invoice. Similar to [Ingress API](#ingress-api).
The main difference with this endpoint is that it is provided the real amount of fiat that the user deposits at the moment that the crypto transaction was confirm. Because the end user cannot be forced to deposit a specific amount of crypto, we cannot rely on what the user input as fiat amount in the beginning of the transaction, because the user can deposit less or more in crypto and due to the variability of the price of this kind of assets.

<aside class="success"><b><code>POST `https://api.onramp.ltd/rpc/create_crypto_ingress_invoice`</code></b></aside>

> Example cURL

```shell
curl https://api.onramp.ltd/rpc/create_crypto_ingress_invoice           \
  -H "x-xco-authorization: Bearer MERCHANT_API_KEY"                   	\
  -H "Content-Type: application/json"                                   \
-X POST -d '{ "crypto_tx_ref"             : "RETURNED_BY_NOTIFICATION_ON_NEW_FUNDS"
              , "payment_ack_url"         : "wwww.example.com"
              , "timeout_in_sec"          : 3600
              , "billing_details"    :
                  { "payer_email"            : "mrpayer.payerson@email.com"
                  , "payer_first_name"       : "MrPayer"
                  , "payer_last_name"        : "Payerson"
                  , "payer_phone"            : "0034666444446"
                  , "birth_date"             : "1980-11-24 00:00:00.000000+00"
                  , "street"                 : "Main Street"
                  , "unit"                   : "Floor 7 12B"
                  , "postal_code"            : "0000001"
                  , "city"                   : "Big Town"
                  , "county"                 : "Big Town county"
                  , "state"                  : "CA"
                  , "prefecture"             : "Prefecture-ku"
                  , "country"                : "GBR"
                  , "kyc_verified"           : "2020-01-01 12:45:00.000000+00"
                  , "kyc_document"           : "Passport"
                  , "kyc_reference"          : "25177aa2-d848-846d-226c-97ec3096f5fe"
                  , "address_verified"       : "2020-01-01 12:55:00.000000+00"
                  , "address_doc_reference"  : "3sdffdss-4mgo-44gfd-34dx-r34rfdfs4gf"
                  , "player_level"           : "New"
                  , "member_since"           : "2020-01-01 12:35:00.000000+00"
                  , "merchant_customer_id"   : "u2340112"
                  }
              }'
```

> Request

```json
{
  "crypto_tx_ref": "RETURNED_BY_NOTIFICATION_ON_NEW_FUNDS",
  "payment_ack_url": "wwww.example.com",
  "timeout_in_sec": 3600,
  "billing_details": {
    "payer_email": "mrpayer.payerson@email.com",
    "payer_first_name": "MrPayer",
    "payer_last_name": "Payerson",
    "payer_phone": "0034666444446",
    "birth_date": "1980-11-24 00:00:00.000000+00",
    "street": "Main Street",
    "unit": "Floor 7 12B",
    "postal_code": "0000001",
    "city": "Big Town",
    "county": "Big Town county",
    "state": "CA",
    "prefecture": "Prefecture-ku",
    "country": "GBR",
    "kyc_verified": "2020-01-01 12:45:00.000000+00",
    "kyc_document": "Passport",
    "kyc_reference": "25177aa2-d848-846d-226c-97ec3096f5fe",
    "address_verified": "2020-01-01 12:55:00.000000+00",
    "address_doc_reference": "3sdffdss-4mgo-44gfd-34dx-r34rfdfs4gf",
    "player_level": "New",
    "member_since": "2020-01-01 12:35:00.000000+00",
    "merchant_customer_id": "u2340112"
  }
}
```

### Authentication

| type   | Header Key            | Header Value              |
| ------ | --------------------- | ------------------------- |
| Header | `x-xco-authorization` | `Bearer merchant_api_key` |

> Response JSON

```json
{
  "invoice_id": "62b570e8-9723-4287-a5a8-e825c2ffced2",
  "invoice_url": "https://wallet.onramp.ltd/ingressing?reference=a3076265-138d-4be6-89fb-d50427adaf4e",
  "invoice_url_qr": "https://api.onramp.ltd/qr_encode/68747470733a2f2f73746167652d77616c6c65742e6f6e72616d702e6c74642f656772657373696e673f7265666572656e63653d38303061343437382d393832322d343561382d616239302d323637306665393262343933",
  "fiat_amount": 1000,
  "fiat_currency": "EUR",
  "crypto_amount": 0.00001,
  "crypto_type": "btc"
}
```

### Request JSON Fields

| Field           | Type            |  Description                                                                                                                        | Required |
| --------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- | -------- |
| crypto_tx_ref   | String          | Reference returned by the `notify_new_funds_url` callback.                                                                          | Yes      |
| payment_ack_url | Url             | Merchant callback endpoint to confirm ingress transaction. It should be a complete, well formed, url.                               | Yes      |
| timeout_in_sec  | Integer         | When to expire the link if unused.                                                                                                  | Yes      |
| billing_details | Billing Details | User billing details. Please, notice how **not** all parameters inside this json object are required except `merchant_customer_id`. | Yes      |

### Billing Details

| Field                 | Type   |  Description                                                          | Required |
| --------------------- | ------ | --------------------------------------------------------------------- | -------- |
| payer_email           | Email  | Email of the user making the payment.                                 | Yes      |
| payer_first_name      | String | First name of the user making the payment.                            | No       |
| payer_last_name       | String | Last name of the user making the payment.                             | No       |
| payer_phone           | String | Including country code, without spaces or separators.                 | No       |
| birth_date            | Date   | Date of birth of the user making the payment.                         | No       |
| street                | String | Street from the billing address.                                      | No       |
| unit                  | String | Unit from the billing address.                                        | No       |
| postal_code           | String | Postal code from the billing address.                                 | No       |
| city                  | String | City from the billing address.                                        | No       |
| county                | String | County from the billing address.                                      | No       |
| state                 | String | State from the billing address.                                       | No       |
| prefecture            | String | Prefecture from the billing address.                                  | No       |
| country               | String | Country from the billing address. Should be a 3 letter ISO Code.      | No       |
| kyc_verified          | Date   | Date when the user was KYC verified.                                  | No       |
| kyc_document          | String | Type of the document. Some examples would be Passport or National_ID. | No       |
| kyc_reference         | String | A unique id of the KYC verification. Something like a UUID.           | No       |
| address_verified      | Date   | Date when the address was verified.                                   | No       |
| address_doc_reference | String | A unique id of the address document reference.                        | No       |
| player_level          | String | Your internal player level. Something like New, Normal, VIP, ...      | No       |
| member_since          | Date   | The date since that user is a member of yours.                        | No       |
| merchant_customer_id  | String | The merchant customer id.                                             | Yes      |

### Response JSON Fields

| Field         | Type    |  Description                                                                                                       |
| ------------- | ------- | ------------------------------------------------------------------------------------------------------------------ |
| fiat_amount   | Integer | Fiat amount in cents denomination after exchange with the current rate of crypto transaction confirmation time.    |
| fiat_currency | String  | Fiat currency. It is the same that the user requested at the beginning of the flow. It is 3 letters ISO 4217 code. |
| crypto_amount | Float   | The amount of crypto that the system received from the user.                                                       |
| crypto_type   | String  | Either "btc" or "bch" for the moment depending on the blockchain that the user selected.                           |
