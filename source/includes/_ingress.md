# Ingress API

## Create Ingress Invoice

> Example Call

```shell
curl -X POST 'https://api.onramp.ltd/rpc/create_ingress_invoice' \
-H 'Content-Type: application/json' \
-H 'x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000' \
-d '{
    "fiat_amount": 3000,
    "fiat_currency": "EUR",
    "payment_ack_url": "https://www.example.com",
    "user_redirect_url": "https://www.example.com?user_redirected",
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
    "first_time": true
}'
```

> Request JSON Body

```json
{
  "fiat_amount": 3000,
  "fiat_currency": "EUR",
  "payment_ack_url": "https://www.example.com",
  "user_redirect_url": "https://www.example.com?user_redirected",
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
  "first_time": true
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

<aside class="success"><b><code> POST /rpc/create_ingress_invoice </code></b></aside>

### Request JSON Fields

| Field                  | Type            |  Description                                                                                                                                                               | Required |
| ---------------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| fiat_amount            | Integer         | Amount to be paid denominated in cents .                                                                                                                                   | Yes      |
| fiat_currency          | String          | Currency identifier following the [ISO 4217 standard](https://en.wikipedia.org/wiki/ISO_4217). Valid values are `"EUR"` or `"USD"`.                                        | Yes      |
| payment_ack_url        | Url             | Merchant callback endpoint to confirm ingress transaction. It should be a complete, well formed, url.                                                                      | Yes      |
| user_redirect_url      | Url             | Where to redirect the user after a successful payment. It should be a complete, well formed, url.                                                                          | Yes      |
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

### Callback Ingress Invoice

Once **ON/RAMP** is ready to commit the payment, it will call back merchant to confirm. **It is only at this point
when the user transaction should be consider done and credited at merchant's side**. If the merchant was able to
credit the user, then it should answer the callback with an http 200 status code; if the merchant was unable to
credit the user, it should answer with an http 204 status code (this will cancel the user payment, expiring his
invoice link). **Any other status code** but 200 or 204 **will be treated as an internal error from the merchant side**,
pausing the user payment and prompting manual intervention, potentially delaying the process several hours.

> IMPORTANT: This callback is going to be done with **HTTP POST** method and not with GET, either for `payment_ack_url` or `cancel_url`.

> Request JSON Body

```json
{
  "reference": "ac400127-93a9-4b9c-9612-c23a3c078933",
  "invoice_id": "62b570e8-9723-4287-a5a8-e825c2ffced2"
}
```

> OR

```json
{
  "reference": "ac400127-93a9-4b9c-9612-c23a3c078933",
  "invoice_id": "62b570e8-9723-4287-a5a8-e825c2ffced2",
  "settlement" {
    "status": "received",
    "data": {
      "name": "John Doe"
    }
  },
  "crypto_info": {
    "currency": "USDT",
    "amount": 100.0,
    "fee_rate": 0.065
  }
}
```

### Confirmation request signature

If you are willing to impose additional security on final confirmation callback (the one specified under `payment_ack_url`) we can provide a unique secret which will be used to sign request in the following way:

- take prettified request body (see [RFC759](https://datatracker.ietf.org/doc/html/rfc7159))
- append as a new line random nonce provided under the header `x-xco-nonce`
- append as a new line given secret
- URI encode result
- apply SH512 hashing
- base64 encode result
- compare obtained string with the one provided under `xco-x-signature` header

> Example confirmation callback with signature header and steps to recreate it (please note that `x-xco-authorization` header will be sent anyway)

```shell
curl -X POST payment_ack_url \
-H 'Content-Type: application/json' \
-H 'x-xco-authorization: Bearer 9d01c660-19e3-4070-a1f1-72a6e2b8bc94' \
-H 'x-xco-nonce: 7d4558fe-04dc-4b95-a71f-c5c1f9e903d4' \
-H 'x-xco-signature: NTRlYTRjYWJmNTk2MmU4Njk2NTZmYjQ0OTAyNTA0OGJhMDEwMmRjMjMyMWJmZGMzN2FiNzczZjBiZmUxYjRjMjZiM2I2ZDRlMDA1NTZiMzZlMWVhNGUxMzRmNGUwZGQ1MTM4MmViNzMyZDg1ZmZjMDcyNmI5MmY1NTQ3MDNlNmI=' \
-d '{
    "reference": "e5b8f01c-bcb1-4721-b831-b2e11903b979",
    "invoice_id": "ebb626aa-5d3b-4642-927f-cb76cbdd350e"
}'
```

> Take prettified JSON Body

```text
{
    "reference": "e5b8f01c-bcb1-4721-b831-b2e11903b979",
    "invoice_id": "ebb626aa-5d3b-4642-927f-cb76cbdd350e"
}
```

> Append nonce and secret (both as new lines)

```text
{
    "reference": "e5b8f01c-bcb1-4721-b831-b2e11903b979",
    "invoice_id": "ebb626aa-5d3b-4642-927f-cb76cbdd350e"
}
7d4558fe-04dc-4b95-a71f-c5c1f9e903d4
NzAwZmIwMGQ0YTJiNDhkMzZjYzc3YjQ5OGQyYWMzOTI=
```

> URI encode

```text
%7B%0A%20%20%20%20%22reference%22%3A%20%22e5b8f01c-bcb1-4721-b831-b2e11903b979%22%2C%0A%20%20%20%20%22invoice_id%22%3A%20%22ebb626aa-5d3b-4642-927f-cb76cbdd350e%22%0A%7D%0A7d4558fe-04dc-4b95-a71f-c5c1f9e903d4%0ANzAwZmIwMGQ0YTJiNDhkMzZjYzc3YjQ5OGQyYWMzOTI%3D
```

> Apply SHA-512

```text
54ea4cabf5962e869656fb449025048ba0102dc2321bfdc37ab773f0bfe1b4c26b3b6d4e00556b36e1ea4e134f4e0dd51382eb732d85ffc0726b92f554703e6b
```

> Encode in base64

```text
NTRlYTRjYWJmNTk2MmU4Njk2NTZmYjQ0OTAyNTA0OGJhMDEwMmRjMjMyMWJmZGMzN2FiNzczZjBiZmUxYjRjMjZiM2I2ZDRlMDA1NTZiMzZlMWVhNGUxMzRmNGUwZGQ1MTM4MmViNzMyZDg1ZmZjMDcyNmI5MmY1NTQ3MDNlNmI=
```

### Bank payments flow

As a clarification, our ingress flow currently supports both paying with credit card and with our bank payments solution.

**In order to be able to use that, you need to get a specific API key from us.**

For how the flow works or what parameters are needed to be used, you can see above on the technical specifications how:

- Flow is exactly the same as in a 3DS transaction, but instead the user seeing an OTP screen to confirm the transaction, they will see a page where they will be able to choose from the available banks and continue the process.
- The parameters required are exactly the same as on the credit card payment, but except for the credit card data since this is a bank payment flow. This means the same current implementation you may have for credit card payment will work (even if for some reason you would be sending credit card details, as those would be ignored).
