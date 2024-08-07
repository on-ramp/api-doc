# Egress crypto flow

This section describes the endpoints used in the egress crypto flow. 

## Get available offers quotes

<aside class="success"><b><code>POST /s2s/egress/quotes?offer_id=a47c2282-7212-5412-ab9a-ee856477648d</code></b></aside>

> Example cURL request


```shell
curl -X POST 'https://api.onramp.ltd/s2s/egress/quotes' \
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

### Request query parameters

| Field           | Type            |  Description                             | Required |
| --------------- | --------------  | ---------------------------------------- | -------- |
| offer_id        | String          | ID of a an offer to return quotes for.   |    No    |

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
        "address": "bc1qu3jehtyxksk6mxqw2r3tug6dda2w9hxm7ehwns"
        }
    },
    "require_confirmation": true,
    "callback_url": "https://www.example.com/callback",
    "quote_id": "baad1363-7cb5-4baa-adae-7ca60535fd76"
}'
```

> Example response

```json
{
  "invoice_id": "10e35c16-3ff8-4238-b5b5-34b4da9b4115"
}
```

### Checks and errors

Before registering an operation, several prerequisite checks will be performed and therefore following errors might be returned:

| Error | HTTP code | Reason      | Resolution |
| ----- | --------- | ----------- | ---------- |
| KYT failed for [chain] address: [address] | 500 | Risk assesment for given address yields High risk score | Not possible to proceed, user migh not be able to withdraw to a given address |
| Amount is too small | 500 | Resulting crypto amount is too small | Retry with a bigger amount |
| Amount is too big | 500 | Resulting crypto amount is too big | Retry with a smaller amount |
| Internal error (CA:0001) | 500 | Internal service failure due to temporary outage or wrong input | Retry, check that crypto address and currency are valid, otherwise contact support |
| Internal error (EE:0001) | 500 | Internal service failure due to temporary outage or wrong input | Retry, check that crypto and fiat currencies are valid, otherwise contact support |

### Request JSON Fields

| Field           | Type            |  Description                 | Required |
| -----------     | --------------  | ---------------------------- | -------- |
| offer_id        | String          | ID of a an offer to use. | Yes      |
| billing_details | Billing Details | User billing details. Please, notice how **not** all parameters inside this JSON object are required except `payer_email`, `payer_phone` and `merchant_customer_id`. | Yes |
| ack_callback    | Url             | Merchant callback endpoint to confirm egress transaction if `require_confirmation`. | No |
| cancel_callback | Url             | Merchant callback endpoint to be called when an egress payment couldn't be completed. | No |
| fiat_amount     | Integer         | Amount to sell denominated in cents. | Yes |
| fiat_currency   | String          | Currency identifier following the [ISO 4217 standard](https://en.wikipedia.org/wiki/ISO_4217). Valid values are `"EUR"` or `"USD"`. | Yes |
| offer_data      | Offer Data      | Object containing offer data. | Yes |
| require_confirmation | Boolean    | Whether the merchant wants to confirm an operation or not. Deafult is `true`. | No |
| callback_url    | Url             | Merchant callback endpoint to be called when status of operation is changed. Currently will be called only when operation is successfully completed. | No |
| quote_id        | String          | Unique ID of a quote, for information purposes only. | No |

### Billing Details

| Field                 | Type   |  Description                                                          | Required |
| --------------------- | ------ | --------------------------------------------------------------------- | -------- |
| payer_email           | Email  | Email of the user making the payment.                                 | Yes      |
| payer_first_name      | String | First name of the user making the payment.                            | No       |
| payer_last_name       | String | Last name of the user making the payment.                             | No       |
| payer_phone           | String | Including country code, without spaces or separators.                 | Yes      |
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

| Field       | Type   |  Description             | Required |
| ----------- | ------ | -------------------------| -------- |
| invoice_id  | String | Unique ID of an invoice. | Yes      |


### Confirmation callback

If merchant is willing to additionally confirm the operation, then:
  - a callback url should be provided in the `ack_callback` field
  - `require_confirmation` field can also be explicitly set to `true` or simply omitted from the request which will result in application of default value whish is `true`
Once **ON/RAMP** is ready to commit the payment, it will call back merchant to confirm. **It is only at this point
when the user transaction should be consider done and be debited at merchant's side**. If the merchant was able to
debit the user, then it should answer the callback with an http 200 status code; if the merchant was unable to
debit the user, it should answer with an http 204 status code (this will cancel the user payment, expiring his
invoice link). **Any other status code** but 200 or 204 **will be treated as an internal error from the merchant side**,
pausing the user payment and prompting manual intervention, potentially delaying the process several hours.

Confirmation callback will be done with the following JSON payload:

```json
{
  "invoice_id": "52e81f50-4bf1-4970-8c53-abe2228e2d5e",
  "reference": "a49c1dee-42b2-427a-a493-8b6e30fab42b"
}
```


> IMPORTANT: This callback is going to be done with **HTTP POST** method and not with GET, either for `payment_ack_url` or `cancel_url`.


### Status callback

In order to receive status callbacks for an operation, a `callback_url` should be provided in the request. **ON/RAMP** will call back the merchant with the following JSON payload:

```json
{
    "invoice_id": "10e35c16-3ff8-4238-b5b5-34b4da9b4115",
    "timestamp_utc": "2020-01-01 12:45:00.000000+00",
    "status": { 
        "completed": {
          "tx_amount": 3.3e-2,
          "tx_hash": "0x3adf55f2aa4eb878169d28db46722770779922ffd551fcaebe5e903f6e29650c",
          "tx_fee": 6.0e-4
          }
    }
}
```

The `status` field can have the following values:
    - `completed`: The payment has been succesfully completed.