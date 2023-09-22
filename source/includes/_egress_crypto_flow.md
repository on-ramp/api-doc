# Egress crypto flow

This section describes the endpoints used in the egress crypto flow. 

## Get available offers quotes

<aside class="success"><b><code>POST /s2s/egress/quotes/{offer_id}</code></b></aside>

> Example cURL request


```shell
curl -X GET 'https://api.onramp.ltd/s2s/egress/quotes' \
-H 'Content-Type: application/json' \
-H 'x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000' \
-d '{
    "currency_lhs": "EUR",
    "currency_rhs": "BTC",
    "amount_lhs": 5000
}'
```

> Example response

```json
[
    {
        "amount_rhs": 3.985893682330214e-3,
        "timestamp_utc": "2023-09-22T12:20:44+00:00",
        "offer_id": "17f5606a-8031-53ba-b660-b029db117188",
        "quote_id": "baad1363-7cb5-4baa-adae-7ca60535fd76"
    }
]
```

### Request JSON Fields

| Field           | Type            |  Description                 | Required |
| --------------- | --------------  | ---------------------------- | -------- |
| currency_lhs    | String          | Currency identifier following the [ISO 4217 standard](https://en.wikipedia.org/wiki/ISO_4217). Valid values are `"EUR"` or `"USD"`. | Yes |
| currency_rhs    | String          | Crypto currency code in upper case. Valid values are `"BTC"`, `"ETH"`, etc. | Yes |
| amount_lhs      | Integer         | Amount to sell. | Yes |


### Response JSON

| Field           | Type            |  Description                                     | Required |
| --------------- | --------------  | ------------------------------------------------ | -------- |
| quote_id        | String          | Unique ID of a quote.                            |   Yes    |
| offer_id        | String          | Offer ID to be used for initiating an operation. |   Yes    |
| amount_rhs      | Float           | Amount to be received.                           |   Yes    |
| timestamp_utc   | Date            | When quote has been received.                    |   Yes    |

## Initiate an egress operation

<aside class="success"><b><code>POST /s2s/egress/create</code></b></aside>

> Example cURL request


```shell
curl -X POST 'https://api.onramp.ltd/s2s/egress/create' \
-H 'Content-Type: application/json' \
-H 'x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000' \
-d '{
    "offer_id": "8869704b-2416-4213-8784-c1e591dc088e",
    "billing_details": {
        "payer_email": "example@example.com",
        "payer_first_name": "John",
        "payer_last_name": "Doe",
        "payer_phone": "0034666444446",
        "birth_date": "1980-11-24 00:00:00.000000+00",
        "street": "Main Street",
        "unit": "Floor 7 12B",
        "postal_code": "0000001",
        "city": "Big Town",
        "county": "Your County",
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
    },
    "ack_callback": "https://www.example.com/callback/ack",
    "cancel_callback": "https://www.example.com/callback/cancel",
    "fiat_amount": 3000,
    "fiat_currency": "EUR",
    "offer_data" : { 
      "crypto_data": {
        "currency": "BTC",
        "address": "1Lbcfr7sAHTD9CgdQo3HTMTkV8LK4ZnX71"
        }
    },
    "require_confirmation": true,
    "callback_url": "https://www.example.com/callback",
    "quote_id": "baad1363-7cb5-4baa-adae-7ca60535fd76"
}'
```

> Example response

```json
  "10e35c16-3ff8-4238-b5b5-34b4da9b4115"
```

### Request JSON Fields

| Field           | Type            |  Description                 | Required |
| -----------     | --------------  | ---------------------------- | -------- |
| offer_id        | String          | ID of a an offer to use. | Yes      |
| billing_details | Billing Details | User billing details. Please, notice how **not** all parameters inside this JSON object are required except `payer_email` and `merchant_customer_id`. | Yes |
| ack_callback    | Url             | Merchant callback endpoint to confirm egress transaction if `require_confirmation`. | No |
| cancel_callback | Url             | Merchant callback endpoint to be called when an egress payment couldn't be completed. | No |
| fiat_amount     | Integer         | Amount to sell denominated in cents. | Yes |
| fiat_currency   | String          | Currency identifier following the [ISO 4217 standard](https://en.wikipedia.org/wiki/ISO_4217). Valid values are `"EUR"` or `"USD"`. | Yes |
| offer_data      | Offer Data      | Object containing offer data. | Yes |
| require_confirmation | Boolean    | Whether the merchant wants to confirm an operation or not. | No |
| callback_url    | Url             | Merchant callback endpoint to be called when status of operation is changed. Currently will be called only when operation is successfully completed. | No |
| quote_id        | String          | Unique ID of a quote, for information purposes only. | No |

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

### Offer Data

| Field       | Type        |  Description                                                   | Required |
| ----------- | ----------- | -------------------------------------------------------------- | -------- |
| crypto_data | Crypto Data | Crypto related information required to complete an operation.  | Yes      |

### Crypto Data

| Field       | Type   |  Description                                                                | Required |
| ----------- | ------ | --------------------------------------------------------------------------- | -------- |
| address     | String | Blockchain address to transfer funds to.                                    | Yes      |
| currency    | String | Crypto currency code in upper case. Valid values are `"BTC"`, `"ETH"`, etc. | Yes      |


### Response JSON

  String containing egress `invoice_id` reference.