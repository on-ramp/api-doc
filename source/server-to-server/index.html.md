---
title: ON/RAMP payment API v1.4.19

version: 1.4.19

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: cURL

#toc_footers:
#  - <a href='https://coinweb.io' target='_blank' rel='noopener'>Sign Up for a Developer Key</a>

includes:
  - server_to_server

search: true

code_clipboard: true
---

# ON/RAMP merchant integration.

## Terminology and Acronyms

- Merchant: The service using **ON/RAMP** as payment processor.
- User: The physical person who sends or receives funds to/from the merchant through **ON/RAMP**.
- CC: credit card.

## Base URL

Production:
**[https://api.onramp.ltd/s2s](https://api.onramp.ltd)**

Test:
**[https://stage-api.onramp.ltd/s2s](https://stage-api.onramp.ltd)**

## Changelog

Changes since v1.4.19:

- Add `first_time` parameter to S2S

Changes since v1.4.18:

- Update Fiat to Crypto Flow

Changes since v1.4.17:

- Added new Fiat to Crypto Flow

Changes since v1.4.16:

- Updated Egress Crypto Flow

Changes since v1.4.15:

- Added new Egress Crypto Flow

Changes since v1.4.14:

- Added new Crypto Ingress Flow and Iframe to work with account based blockchains

Changes since v1.4.13:

- Added `first_time` parameter on ingress flow

Changes since v1.4.12:

- Added `x_api_key` parameter on the crypto ingress iframe flow

Changes since v1.4.11:

- Specified parameters required for new bank payment

Changes since v1.4.10:

- Added `card_type` to ingress flow parameters
- Added `order_id` to ingress flow parameters

Changes since v1.4.9:

- Made street and city fields required

Changes since v1.4.8:

- Updated confirmation callback

Changes since v1.4.7:

- Docummented the addition of `op_id` for `create_operation` endpoint

Changes since v1.4.6:

- Updated some fields for 3DS

Changes since v1.4.5:

- Updated some fields for 3DS

Changes since v1.4.4:

- Updated 3ds values

# Calling **ON/RAMP** API endpoints

**ON/RAMP** offers a simple REST API taking JSON values as request payload, and returning JSON
values as response.

All calls shall be made using **`POST`** method.

Every call shall include the headers:

- `Content-Type: application/json`

- `X-XCO-Authorization: Bearer AUTH_TOKEN`

Where `AUTH_TOKEN` is replaced with merchant's API key.

## Error handling

All ON/RAMP endpoints respect standard HTTP semantics. Errors can be detected from response
status code:

| Status code | Meaning                                        |
| ----------- | ---------------------------------------------- |
| **200**     | Everything was ok. Success.                    |
| **400**     | Invalid request parameters. See response body. |
| **500**     | Temporary ON/RAMP server malfunction.          |

Requests resulting in 4xx errors **must not be retried** as-is, that will not resolve the error.

Requests giving 5xx errors may be retried. If retried, the retry interval must multiplicatively
increase in geometric progression, using [exponential backoff][]. E.g. retry 1 after 160ms, retry 2
after 320ms, retry 3 after 640ms, retry 4 after 1280ms, and so on.

[exponential backoff]: https://en.wikipedia.org/wiki/Exponential_backoff

## Webhook callbacks

Some endpoints involve _callbacks:_ ON/RAMP system will sometimes make requests to
Merchant-provided URLs.

All callbacks invoked by ON/RAMP are made using **`POST`** method.

In callback response, any HTTP status code besides 200 **is treated as callback failure**.
