# Egress fiat flow

## Get known user cards

<aside class="success"><b><code>GET /s2s/egress/fiat/cards?customer_id=00112233-4455-6677-8899-aabbccddeeff</code></b></aside>

> Example cURL request


```shell
curl -X GET 'https://api.onramp.ltd/s2s/egress/fiat/cards?customer_id=95e147d0-7935-4ca5-9ebd-68e04b1ebc28' \
-H 'Content-Type: application/json' \
-H 'x-xco-authorization: Bearer 83d00b66-8dc5-4cda-b8f1-6e608ece6835' \
```

> Example response

```json
[
    {
        "id": "67c31893-e56c-401a-8357-d08eae691323",
        "at": "2024-07-12T16:27:45.469322961Z",
        "card":
            {
                "iban": null,
                "name": null,
                "accountNumber": null,
                "sortCode": null
            }
    },
    {
        "id": "c53c92b6-fada-4b77-b6fc-a601943ccf11",
        "at": "2024-08-14T11:54:14.41871715Z",
        "card":
            {
                "iban": null,
                "name": null,
                "accountNumber": null,
                "sortCode": null
            }
    }
]
```

### Request query parameters

| Field           | Type            |  Description                             | Required |
| --------------- | --------------  | ---------------------------------------- | -------- |
| customer_id     | String          | Customer identifier.                     |   Yes    |

### Response JSON

| Field           | Type            |  Description                                | Required |
| --------------- | --------------  | ------------------------------------------- | -------- |
| id              | UUID            | Card identifier in our system.              |   Yes    |
| at              | UTC timestamp   | When the card was used.                     |   Yes    |
| card            | JSON value      | Card information.                           |   Yes    |

## Initiate an egress operation

<aside class="success"><b><code>POST /s2s/egress/fiat/create</code></b></aside>

> Example cURL request


```shell
curl -X POST 'https://api.onramp.ltd/s2s/egress/fiat/create' \
-H 'Content-Type: application/json' \
-H 'x-xco-authorization: Bearer 83d00b66-8dc5-4cda-b8f1-6e608ece6835' \
-d '{
    "customer_id": "95e147d0-7935-4ca5-9ebd-68e04b1ebc28",
    "card_id": "67c31893-e56c-401a-8357-d08eae691323",
    "fiat_amount": 1500,
    "fiat_currency": "EUR",
    "callback_url": "https://example.com",
    "require_confirmation": true
}'
```

> Example response

```json
{
  "id": "3aa38504-d6fe-4d11-9635-563e098ed79c"
}
```

### Request JSON Fields

| Field                | Type                | Description                                                     | Required |
| -------------------- | ------------------- | --------------------------------------------------------------- | -------- |
| customer_id          | String              | Customer identifier.                                            | Yes      |
| card_id              | UUID                | Card identifier in our system.                                  | Yes      |
| fiat_currency        | ISO 4217 alpha code | Currency in which the transaction should occur.                 | Yes      |
| fiat_amount          | Integer             | Amount, in minor units of the provided fiat_currency.           | Yes      |
| callback_url         | URL                 | Endpoint to notify when transaction completes.                  | Yes      |
| require_confirmation | Boolean             | Whether the transaction requires manual approval from our side. | No       |

### Confirmation callback

Confirmation callback is performed as a **POST** HTTP request with the following JSON payload:

```json
{
    "reference": "3aa38504-d6fe-4d11-9635-563e098ed79c",
    "outcome": "success" // or "failure"
}
```

Response must have a status of **HTTP 200**. Any other status, or failure to process
the callback, will result in a callback retry with exponential backoff.
