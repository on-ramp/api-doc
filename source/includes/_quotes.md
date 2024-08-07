# Quotes API

In case you are getting crypto in return of an [ingress](/#ingress-api), you can get the reference prices for it if you would like to display that to users at the beginning of the process.

An API key is required to use this API.  Please contact us to request an API key.

## Base URL

Production:
**[https://web.blockfort-exchange.io](https://web.blockfort-exchange.io)**

Test:
**[https://kac8t1chv8.execute-api.eu-west-2.amazonaws.com](https://kac8t1chv8.execute-api.eu-west-2.amazonaws.com)**

## Get reference prices

> Example Call

```shell
curl -X POST 'https://web.blockfort-exchange.io/referenceTrade' \
-H 'Content-Type: application/json' \
-H 'x-api-key: 0000000000000000000000000000000000000000' \
-d '{
    "currency_lhs":"gbp",
    "currency_rhs":"usdt",
    "amount_lhs":100,
    "direction":"sell"
}'
```

> Request JSON Body

```json
{
  "currency_lhs": "gbp",
  "currency_rhs": "usdt",
  "amount_lhs": 100,
  "direction": "sell"
}
```

> Response JSON

```json
{
  "currency_lhs": "gbp",
  "currency_rhs": "usdt",
  "amount_lhs": -100,
  "amount_rhs": 134.37825813749998,
  "exchange_rate": 1.343782581375,
  "timestamp_utc": "2021-09-03T04:16:14"
}
```

### HTTP Request

<aside class="success"><b><code> POST /referenceTrade </code></b></aside>

### Request JSON Fields

| Field        | Type    | Description                                                                                                                                  | Required |
| ------------ | ------- | -------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| currency_lhs | String  | Left hand side currency.                                                                                                                     | Yes      |
| currency_rhs | String  | Right hand side currency.                                                                                                                    | Yes      |
| amount_lhs   | Integer | Amount to buy (if positive) or sell (if negative) of the left hand side currency. Sign will be ignored if the "direction" parameter is sent. | Yes      |
| direction    | String  | Optional parameter that indicates if the results are going to be about buying or selling the left hand side currency.                        | No       |
