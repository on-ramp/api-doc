# Crypto ingress flow

This section describes the endpoints used in the crypto ingress flow. They are also used in the [**iframe** integration](#crypto-iframe-integration).

Follow these steps when using this flow:

1. Initiate a crypto ingress operation.  There are 2 options depending on the type of blockchains used for the transaction:
    1. UTXO based blockchains, e.g., Bitcoin: request an address to which coins will be transferred.
    2. Account-based blockchains, e.g. Ethereum or Tron: register the address from which coins/tokens will be transferred. After a transaction has been made, register the transaction details.
2. Once the transaction has been confirmed, a notification request will be sent to a URL provided by the merchant.
3. Upon receiving the notification, the merchant is expected to send a request to initiate an invoice.

## Supported blockchains

| Name         | Symbol |
| ------------ | ------ |
| Bitcoin      | `btc`  |
| Bitcoin Cash | `bch`  |
| Ethereum     | `eth`  |
| Tron         | `trx`  |

## Supported coins / tokens

| Coin / Token | Blockchains  |
| ------------ | ------------ |
| `btc`        | `btc`        |
| `bch`        | `bch`        |
| `eth`        | `eth`        |
| `usdt`       | `eth`, `trx` |
| `usdc`       | `eth`, `trx` |

## Crypto ingress base URL

There are 2 sets of base URLs used for crypto ingress flow:

### Main API base URL

Production:
**[https://api.onramp.ltd](https://api.onramp.ltd)**

Test:
**[https://stage-api.onramp.ltd](https://stage-api.onramp.ltd)**


The above base URLs can be used for all endpoints *except* the legacy crypto endpoint:

`POST /blockchain/api/v1/{chain}/address/new`


### Legacy crypto API base URL

Production:
**[https://crypto.onramp.ltd](https://crypto.onramp.ltd)**

Test:
**[https://stage-crypto.onramp.ltd](https://stage-crypto.onramp.ltd)**

The above base URLs should only be used for the legacy crypto endpoint:

`POST /blockchain/api/v1/{chain}/address/new`

Please see [Request address for UTXO-based blockchains](#request-address-for-utxo-based-blockchains)

## Request address for UTXO-based blockchains

This endpoint should only be used for UTXO-based blockchains, e.g., Bitcoin or Bitcoin Cash.  For account-based blockchains, please refer to [Request address for account-based blockchains and tokens](#request-address-for-account-based-blockchains-and-tokens)

A receiving address will be returned once a successful request is made to this endpoint.

<aside class="success"><b><code>POST /blockchain/api/v1/{chain}/address/new</code></b></aside>

> Example cURL

```shell
curl -X POST 'https://crypto.onramp.ltd/blockchain/api/v1/btc/address/new' \
-H 'Content-Type: application/json' \
-H 'x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000' \
-d '{
    "merchant_customer_id": "u2340112",
    "fiat_amount": 10000,
    "fiat_currency": "EUR",
    "notify_new_funds_url": "http://mydomain.com/callback/notify/crypto_done",
    "email": "example@example.com",
    "merchant_reference_id": "MERCHANT_REF_ID_1"
}'
```

> Request JSON Body

```json
{
  "merchant_customer_id": "u2340112",
  "fiat_amount": 10000,
  "fiat_currency": "EUR",
  "notify_new_funds_url": "http://mydomain.com/callback/notify/crypto_done",
  "email": "example@example.com",
  "merchant_reference_id": "MERCHANT_REF_ID_1"
}
```

> Response JSON

```json
{
  "address": "BITCOIN_ADDRESS_1",
  "chain": "btc",
  "qr_base64": "data:image/png;base64,AAAA..."
}
```

### Request URL Parameters

| Field | Type   | Description                                        | Required |
| ----- | ------ | -------------------------------------------------- | -------- |
| chain | String | Blockchain symbol.  Possible values: `btc`, `bch`. | Yes      |


### Request JSON Fields

| Field                 | Type    | Description                                                                            | Required |
| --------------------- | --------| -------------------------------------------------------------------------------------- | -------- |
| merchant_customer_id  | String  | The merchant customer id.                                                              | Yes      |
| fiat_amount           | Integer | Amount expected to be paid denominated in cents.                                       | Yes      |
| fiat_currency         | String  | Currency identifier following the ISO 4217 standard.                                   | Yes      |
| notify_new_funds_url  | Url     | Callback URL to which a request will be sent after the transaction has been confirmed. | Yes      |
| email                 | Email   | Email of the customer.                                                                 | No       |
| merchant_reference_id | String  | A reference id for the merchant, e.g., internal transaction id.                        | No       |

### Response JSON Fields

| Field     | Type   | Description                                     |
| --------- | ------ | ----------------------------------------------- |
| address   | String | Blockchain address to which funds will be sent. |
| chain     | String | Blockchain symbol, e.g., `btc`, `bch`.          |
| qr_base64 | String | QR code data URL.                               |

## Request address for account-based blockchains and tokens

This endpoint should only be used for account-based blockchains, e.g., Ethereum or Tron.  For UTXO-based blockchains, please refer to [Request address for UTXO-based blockchains](#request-address-for-utxo-based-blockchains)

A receiving address will be returned along with a reference ID once a successful request is made to this endpoint.

After a transaction has been made, register the transaction with [Register transaction for account-based blockchains and tokens](#register-transaction-for-account-based-blockchains-and-tokens)

<aside class="success"><b><code>POST /blockchain/api/v2/transaction</code></b></aside>

> Example cURL

```shell
curl -X POST 'https://api.onramp.ltd/blockchain/api/v2/transaction' \
-H 'Content-Type: application/json' \
-H 'x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000' \
-d '{
    "merchant_customer_id": "u2340112",
    "fiat_amount": 10000,
    "fiat_currency": "EUR",
    "sender_address": "ETHEREUM_ADDRESS_1",
    "chain": "eth",
    "notify_new_funds_url": "http://mydomain.com/callback/notify/crypto_done",
    "notify_cancellation_url": "http://mydomain.com/callback/notify/crypto_canceled",
    "email": "example@example.com",
    "merchant_reference_id": "MERCHANT_REF_ID_1",
    "require_unique_recipient_address": false,
    "is_fixed_price": false,
    "use_external_wallet": true
}'
```

> Request JSON Body

```json
{
  "merchant_customer_id": "u2340112",
  "fiat_amount": 10000,
  "fiat_currency": "EUR",
  "sender_address": "ETHEREUM_ADDRESS_1",
  "chain": "eth",
  "notify_new_funds_url": "http://mydomain.com/callback/notify/crypto_done",
  "notify_cancellation_url": "http://mydomain.com/callback/notify/crypto_canceled",
  "email": "example@example.com",
  "merchant_reference_id": "MERCHANT_REF_ID_1",
  "require_unique_recipient_address": false,
  "is_fixed_price": false,
  "use_external_wallet": true
}
```
> Response JSON

```json
{
  "address": "ETHEREUM_ADDRESS_2",
  "chain": "eth",
  "qr_base64": "data:image/png;base64,AAAA...",
  "xref": "11111111-1111-1111-1111-111111111111"
}
```

### Request JSON Fields

| Field                            | Type    | Description                                                                            | Required |
| -------------------------------- | --------| -------------------------------------------------------------------------------------- | -------- |
| merchant_customer_id             | String  | The merchant customer id.                                                              | Yes      |
| fiat_amount                      | Integer | Amount expected to be paid denominated in cents.                                       | Yes      |
| fiat_currency                    | String  | Currency identifier following the ISO 4217 standard.                                   | Yes      |
| sender_address                   | String  | Blockchain public address of the sender.                                               | Yes      |
| chain                            | String  | Blockchain symbol.  Possible values: `eth`, `trx`.                                     | Yes      |
| notify_new_funds_url             | Url     | Callback URL to which a request will be sent after the transaction has been confirmed. | Yes      |
| notify_cancellation_url          | Url     | Callback URL to which a request will be sent after the transaction has been canceled.  | No       |
| email                            | Email   | Email of the customer.                                                                 | No       |
| merchant_reference_id            | String  | A reference id for the merchant, e.g., internal transaction id.                        | No       |
| require_unique_recipient_address | Boolean | `true` if cannot register transaction in the next step. Otherwise, `false`.            | No       |
| is_fixed_price                   | Boolean | `true` if requires fixed-price processing.  Otherwise, `false`.                        | No       |
| use_external_wallet              | Boolean | `true` if `is_fixed_price` is `true` or `require_unique_recipient_address` is `true`   | No       |

### Response JSON Fields

| Field     | Type   | Description                                     |
| --------- | ------ | ----------------------------------------------- |
| address   | String | Blockchain address to which funds will be sent. |
| chain     | String | Blockchain, e.g., `eth`, `trx`.                 |
| qr_base64 | String | QR code data URL.                               |
| xref      | String | Reference ID.                                   |


## Register transaction for account-based blockchains and tokens

This endpoint should only be used for account-based blockchains, e.g., Ethereum or Tron.  For UTXO-based blockchains, please refer to [Request address for UTXO-based blockchains](#request-address-for-utxo-based-blockchains)

After a transaction has been made, send a request to this endpoint in order to register the transaction.  A reference ID, required for this request, is provided in the response from [Request address for account-based blockchains and tokens](#request-address-for-account-based-blockchains-and-tokens).

<aside class="success"><b><code>PATCH /blockchain/api/v2/transaction</code></b></aside>

> Example cURL

```shell
curl -X PATCH 'https://api.onramp.ltd/blockchain/api/v2/transaction' \
-H 'Content-Type: application/json' \
-H 'x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000' \
-d '{
    "xref": "11111111-1111-1111-1111-111111111111",
    "merchant_customer_id": "u2340112",
    "fiat_currency": "EUR",
    "sender_address": "ETHEREUM_ADDRESS_1",
    "chain": "eth",
    "tx_hash": "TRANSACTION_HASH_1",
    "tx_amount": 110,
    "token": "usdt",
    "use_external_wallet": true
}'
```

> Request JSON Body

```json
{
  "xref": "11111111-1111-1111-1111-111111111111",
  "merchant_customer_id": "u2340112",
  "fiat_currency": "EUR",
  "sender_address": "ETHEREUM_ADDRESS_1",
  "chain": "eth",
  "tx_hash": "TRANSACTION_HASH_1",
  "tx_amount": 110,
  "token": "usdt",
  "use_external_wallet": true
}
```
> Response JSON

```json
{
  "xref": "11111111-1111-1111-1111-111111111111"
}
```

### Request JSON Fields

| Field                | Type    | Description                                                                              | Required |
| -------------------- | --------| ---------------------------------------------------------------------------------------- | -------- |
| xref                 | String  | Reference ID received when request for a receiving address.                              | Yes      |
| merchant_customer_id | String  | The merchant customer id.                                                                | Yes      |
| fiat_currency        | String  | Currency identifier following the ISO 4217 standard.                                     | Yes      |
| sender_address       | String  | Blockchain public address of the sender.                                                 | Yes      |
| chain                | String  | Blockchain symbol. Possible values: `eth`, `trx`.                                        | Yes      |
| tx_hash              | String  | Blockchain transaction hash.                                                             | Yes      |
| tx_amount            | String  | Amount in token unit sent in the transaction.                                            | Yes      |
| token                | String  | Token sent in the transaction, e.g., `usdt`, `usdc`. Not required for native coin/token. | No       |
| use_external_wallet  | Boolean | Must match the value used previously when request address.                               | No       |

### Response JSON Fields

| Field | Type   | Description  |
| ----- | ------ | -------------|
| xref  | String | Reference ID |


## Notification of successful crypto transaction

A URL provided by the merchant in a previous request will be used to send a notification request once the transaction has been confirmed.

**_IMPORTANT_**: This is not the same as `payment_ack_url`. It should NOT be considered as an INVOICE.

<aside class="success"><b><code>POST notify_new_funds_url</code></b></aside>

> Example cURL

```shell

curl -X POST notify_new_funds_url \
-H 'Content-Type: application/json' \
-H 'x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000' \
-d '{
    "crypto_tx_ref": "11111111-1111-1111-1111-111111111111",
    "high_risk": false
}'
```

> Request JSON Body

```json
{
  "crypto_tx_ref": "11111111-1111-1111-1111-111111111111",
  "high_risk": false
}
```

### Request JSON Fields

| Field                | Type    | Description                                                  |
| -------------------- | --------| ------------------------------------------------------------ |
| crypto_tx_ref        | String  | Reference ID to be used when creating an invoice.            |
| high_risk            | Boolean | `true` if the transaction of high risk.  Otherwise, `false`. |


### Expected Response

| Status |
| ------ |
| 200    |

## Create crypto ingress invoice

Once the merchant receives a notification that a successful transaction has been made, a request should be sent to this endpoint to create an invoice.

A reference ID, required for this request, is provided in the [Notification of successful crypto transaction](#notification-of-successful-crypto-transaction)

Please note that the payment amount is absent from the request to this endpoint.  This is because the payment amount depends on what is transferred by the user, and not necessarily the amount provided during initiation of the crypto ingress operation.

<aside class="success"><b><code>POST `https://api.onramp.ltd/rpc/create_crypto_ingress_invoice`</code></b></aside>

> Example cURL

```shell
curl -X POST 'https://api.onramp.ltd/rpc/create_crypto_ingress_invoice' \
-H 'x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000' \
-H 'Content-Type: application/json' \
-d '{
    "crypto_tx_ref": "RETURNED_BY_NOTIFICATION_ON_NEW_FUNDS",
    "payment_ack_url": "https://www.example.com",
    "timeout_in_sec": 3600,
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
        "address_doc_reference" : "3sdffdss-4mgo-44gfd-34dx-r34rfdfs4gf",
        "player_level": "New",
        "member_since": "2020-01-01 12:35:00.000000+00",
        "merchant_customer_id": "u2340112"
    }
}'
```

> Request JSON Body

```json
{
  "crypto_tx_ref": "RETURNED_BY_NOTIFICATION_ON_NEW_FUNDS",
  "payment_ack_url": "htts://www.example.com",
  "timeout_in_sec": 3600,
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
  }
}
```

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

| Field           | Type            |  Description                                                                      | Required |
| --------------- | --------------- | --------------------------------------------------------------------------------- | -------- |
| crypto_tx_ref   | String          | Reference returned by the `notify_new_funds_url` callback.                        | Yes      |
| payment_ack_url | Url             | Callback URL to which a request will be sent when ingress is finalized.           | Yes      |
| timeout_in_sec  | Integer         | Time in seconds before `payment_ack_url` expires.                                 | Yes      |
| billing_details | Billing Details | User billing details. Please refer to "Billing Details" for available parameters. | Yes      |

### Billing Details

| Field                 | Type   |  Description                                                          | Required |
| --------------------- | ------ | --------------------------------------------------------------------- | -------- |
| payer_email           | Email  | Email of the user making the payment.                                 | Yes      |
| payer_first_name      | String | First name of the user making the payment.                            | No       |
| payer_last_name       | String | Last name of the user making the payment.                             | No       |
| payer_phone           | String | Phone number including country code, without spaces or separators.    | No       |
| birth_date            | Date   | Date of birth of the user making the payment.                         | No       |
| street                | String | Street from the billing address.                                      | No       |
| unit                  | String | Unit from the billing address.                                        | No       |
| postal_code           | String | Postal code from the billing address.                                 | No       |
| city                  | String | City from the billing address.                                        | No       |
| county                | String | County from the billing address.                                      | No       |
| state                 | String | State from the billing address.                                       | No       |
| prefecture            | String | Prefecture from the billing address.                                  | No       |
| country               | String | A 3-letter ISO 3166 country code from the billing address.            | No       |
| kyc_verified          | Date   | Date when the user was KYC verified.                                  | No       |
| kyc_document          | String | Type of the document, e.g., `Passport` or `National_ID`.              | No       |
| kyc_reference         | String | A unique id of the KYC verification.                                  | No       |
| address_verified      | Date   | Date when the address was verified.                                   | No       |
| address_doc_reference | String | A unique id of the address document reference.                        | No       |
| player_level          | String | Merchant's internal player level, e.g., `New`, `Normal`, `VIP`.       | No       |
| member_since          | Date   | The date since the user has become a member.                          | No       |
| merchant_customer_id  | String | The merchant customer id.                                             | Yes      |

### Response JSON Fields

| Field          | Type    |  Description                                                                                                     |
| -------------- | ------- | ---------------------------------------------------------------------------------------------------------------- |
| invoice_id     | String  | Invoice identifier.                                                                                              |
| invoice_url    | Url     | Invoice URL to which user can be redirected.                                                                     |
| invoice_url_qr | Url     | Source URL for an image with QR-encoded `invoice_url`.                                                           |
| fiat_amount    | Integer | Fiat amount in cents denomination converted from `crypto_amount` with the rate at transaction confirmation time. |
| fiat_currency  | String  | A 3-letter ISO 4217 code fiat currency provided when crypto ingress operation was initiated.                     |
| crypto_amount  | Float   | The amount of crypto that the system received from the user.                                                     |
| crypto_type    | String  | Coin/token, e.g., `btc`, `eth`, `usdt`.                                                                          |
