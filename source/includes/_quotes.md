# Quotes API

In case you are getting crypto in return of an [ingress](/#ingress-api), you can get the reference prices for it if you would like to display that to users at the beginning of the process.

## Base URL

Production and Test:
**[https://api.blockfort-exchange.io](https://api.blockfort-exchange.io)**

## Get reference prices

> Example Call

```shell
curl -X GET \
	'https://api.blockfort-exchange.io/referenceTrade' \
	-H 'x-api-key: QU0J2HfUMH0r2Yh3I3EhRyK4qRx30likZ7dBRsP8' \
	-H 'Content-Type: application/json' \
	-d '{
			"currency_lhs":"gbp",
			"currency_rhs":"usdt",
			"amount_lhs":-100
		}'
```

> Request JSON Body

```json
{
	"currency_lhs": "gbp",
	"currency_rhs": "usdt",
	"amount_lhs": -100
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

<aside class="success"><b><code> GET /referenceTrade </code></b></aside>

### Request JSON Fields

| Field        | Type    | Description                                                                       | Required |
| ------------ | ------- | --------------------------------------------------------------------------------- | -------- |
| currency_lhs | Integer | Left hand side currency.                                                          | Yes      |
| currency_rhs | Integer | Right hand side currency.                                                         | Yes      |
| amount_lhs   | Integer | Amount to buy (if positive) or sell (if negative) of the left hand side currency. | Yes      |
