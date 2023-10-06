# Fiat to Crypto flow

This section describes the endpoints used in the fiat to crypto flow. 

## Create Fiat to Crypto Invoice

> Example Call

```shell
curl -X POST 'https://api.onramp.ltd/exchange/fiat/crypto/invoice/new' \
-H 'Content-Type: application/json' \
-H 'x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000' \
-d '{
    "fiat_amount": 3000,
    "fiat_currency": "EUR",
    "payment_ack_url": "https://www.example.com",
    "user_redirect_url": "https://www.example.com?user_redirected",
    "callback_url": "https://www.example.com",
    "timeout_in_sec": 3600,
    "offer_skin": {
        "title": "The Nice Merchant",
        "image": "https://static.example.com/merchant-logo",
        "description": "Awesome green Pluff with handler"
    },
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
    "card_number": "4929421234600821",
    "card_expiry_date": "2030-12-01",
    "card_cvv": "356",
    "card_holder_first_name": "John",
    "card_holder_last_name": "Doe",
    "card_type": "VISA",
    "order_id": "order-1234",
    "first_time": true,
    "is_banking": false,
    "crypto_data": {
        "currency": "BTC",
        "address": "1Lbcfr7sAHTD9CgdQo3HTMTkV8LK4ZnX71"
    }
}'
```

> Request JSON Body

```json
{
  "fiat_amount": 3000,
  "fiat_currency": "EUR",
  "callback_url": "https://www.example.com",
  "timeout_in_sec": 3600,
  "offer_skin": {
    "title": "The Nice Merchant",
    "image": "https://static.example.com/merchant-logo",
    "description": "Awesome green Pluff with handler"
  },
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
  "card_number": "4929421234600821",
  "card_expiry_date": "2030-12-01",
  "card_cvv": "356",
  "card_holder_first_name": "John",
  "card_holder_last_name": "Doe",
  "card_type": "VISA",
  "order_id": "order-1234",
  "first_time": true,
  "is_banking": false,
  "crypto_data": {
    "currency": "BTC",
    "address": "1Lbcfr7sAHTD9CgdQo3HTMTkV8LK4ZnX71"
  }
}
```

> Response JSON

```json
[
  {
    "invoice_id": "62b570e8-9723-4287-a5a8-e825c2ffced2",
    "invoice_url": "https://wallet.onramp.ltd/ingressing?reference=a3076265-138d-4be6-89fb-d50427adaf4e",
    "invoice_url_qr": "https://api.onramp.ltd/qr_encode/68747470733a2f2f73746167652d77616c6c65742e6f6e72616d702e6c74642f656772657373696e673f7265666572656e63653d38303061343437382d393832322d343561382d616239302d323637306665393262343933"
  }
]
```

### HTTP Request

<aside class="success"><b><code> POST /rpc/create_fiat_to_crypto_invoice </code></b></aside>

### Request JSON Fields

| Field                  | Type            |  Description                                                                                                                                                               | Required |
| ---------------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| fiat_amount            | Integer         | Amount to be paid denominated in cents .                                                                                                                                   | Yes      |
| fiat_currency          | String          | Currency identifier following the [ISO 4217 standard](https://en.wikipedia.org/wiki/ISO_4217). Valid values are `"EUR"` or `"USD"`.                                        | Yes      |
| callback_url           | Url             | Callback to receive status updates. It should be a complete, well formed, url.                                                                      | No      |
| timeout_in_sec         | Integer         | When to expire the link if unused.                                                                                                                                         | Yes      |
| offer_skin             | Ingress Skin    | Specify how the offer should be displayed to the user.                                                                                                                     | Yes      |
| billing_details        | Billing Details | User billing details. Please, notice how **not** all parameters inside this json object are required except `merchant_customer_id`.                                        | Yes      |
| cancel_callback        | Url             | Merchant callback endpoint to be called when an ingress payment couldn't be completed.                                                                                     | No       |
| card_number            | String          | Credit card number to be used to attempt the payment. If not sent, no credit card details will be prefilled. **Not needed for bank payments**                              | No       |
| card_expiry_date       | String          | Credit card expiry date to be used to attempt the payment. **Not needed for bank payments**                                                                                | No       |
| card_cvv               | String          | Credit card CVV to be used to attempt the payment. **Not needed for bank payments**                                                                                        | No       |
| card_holder_first_name | String          | Credit card cardholder first name to be used to attempt the payment. **Not needed for bank payments**                                                                      | No       |
| card_holder_last_name  | String          | Credit card cardholder last name to attempt the payment. **Not needed for bank payments**                                                                                  | No       |
| card_type              | String          | Credit card type to be used to attempt the payment. Please consult our integration team for supported values before using this parameter. **Not needed for bank payments** | No       |
| order_id               | String          | Merchant order id. Used to link back ingress opeartion when exporitng reports via backoffice.                                                                              | No       |
| first_time             | Boolean         | Whether the invoice is for a first time deposit.                                | No       |
| is_banking             | Boolean         | Whether the invoice should go via bank payments solution. Deafult is `false`.                               | No       |
| crypto_data            | Crypto Data     | Object containing crypto data.                                                                                                                                             | Yes      |

### Ingress Skin

| Field       | Type   | Description                                           | Required |
| ----------- | ------ | ----------------------------------------------------- | -------- |
| title       | String | Short string containing merchant's or product's name. | Yes      |
| image       | Url    | image to stylize the offer.                           | Yes      |
| description | String | A text explaining what the user is purchasing.        | Yes      |

### Billing Details

| Field                 | Type   | Description                                                                     | Required |
| --------------------- | ------ | ------------------------------------------------------------------------------- | -------- |
| payer_email           | Email  | Email of the user making the payment.                                           | Yes      |
| payer_first_name      | String | First name of the user making the payment.                                      | No       |
| payer_last_name       | String | Last name of the user making the payment.                                       | No       |
| payer_phone           | String | Including country code, without spaces or separators.                           | No       |
| birth_date            | Date   | Date of birth of the user making the payment.                                   | No       |
| street                | String | Street from the billing address.                                                | No       |
| unit                  | String | Unit from the billing address.                                                  | No       |
| postal_code           | String | Postal code from the billing address.                                           | No       |
| city                  | String | City from the billing address.                                                  | No       |
| county                | String | County from the billing address.                                                | No       |
| state                 | String | State from the billing address.                                                 | No       |
| prefecture            | String | Prefecture from the billing address.                                            | No       |
| country               | String | Country from the billing address. Should be a 3 letter ISO Code.                | Yes      |
| kyc_verified          | Date   | Date when the user was KYC verified. **Important: Please check section below.** | No       |
| kyc_document          | String | Type of the document. Some examples would be Passport or National_ID.           | No       |
| kyc_reference         | String | A unique id of the KYC verification. Something like a UUID.                     | No       |
| address_verified      | Date   | Date when the address was verified.                                             | No       |
| address_doc_reference | String | A unique id of the address document reference.                                  | No       |
| player_level          | String | Your internal player level. Something like New, Normal, VIP, ...                | No       |
| member_since          | Date   | The date since that user is a member of yours.                                  | No       |
| merchant_customer_id  | String | The merchant customer id.                                                       | Yes      |

### Crypto Data

| Field    | Type   | Description                                                                 | Required |
| -------- | ------ | --------------------------------------------------------------------------- | -------- |
| currency | String | Currency identifier following the [ISO 4217 standard](https://en.wikipedia.org/wiki/ISO_4217). Valid values are `"BTC"` or `"ETH"`. | Yes      |
| address  | String | Blockchain address to transfer funds to.                                     | Yes      |


### Response JSON Fields

| Field          | Type   | Description                                           |
| -------------- | ------ | ----------------------------------------------------- |
| invoice_id     | String | Internal **ON/RAMP**'s Invoice Identifier.            |
| invoice_url    | Url    | Url where to redirect user.                           |
| invoice_url_qr | Url    | Source Url for an image with QR-encoded `invoice_url` |

### Verified vs Unverified traffic

**One of the most important things to be defined when communicating with the ON/RAMP API is if the traffic you are sending is verified or unverified.**

In order to let us know if that specific user you are sending us for a transaction has been KYC verified is to send a valid date on the `kyc_verified` parameter inside the `billing_details` object.

So the 2 potential cases are:

- Verified traffic

  - You need to send us the `kyc_verified` parameter with a valid date.

- Unverified traffic

  - You can either not send the `kyc_verified` parameter or send it with a `null` value.


### Status callback

In order to receive status callbacks for an operation, a `callback_url` should be provided in the request. **ON/RAMP** will call back the merchant with the following JSON payload:

```json
{
    "invoice_id": "10e35c16-3ff8-4238-b5b5-34b4da9b4115",
    "timestamp_utc": "2020-01-01 12:45:00.000000+00",
    "status": { 
        "completed": {}
    }
}
```

> IMPORTANT: This callback is going to be done with **HTTP POST** method and not with GET.

The `status` field can have the following values:
    - `completed`: The payment has been succesfully completed.
