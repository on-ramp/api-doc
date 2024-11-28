---
title: ON/RAMP payment API v1.4.24

version: 1.4.24

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: cURL

#toc_footers:
#  - <a href='https://coinweb.io' target='_blank' rel='noopener'>Sign Up for a Developer Key</a>

includes:
  - ingress
  - egress
  - lifecycle
  - iframe
  - crypto
  - crypto_flow
  - quotes
  - egress_crypto_flow
  - fiat_to_crypto_flow

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
**[https://api.onramp.ltd/rpc](https://api.onramp.ltd)**

Test:
**[https://stage-api.onramp.ltd/rpc](https://stage-api.onramp.ltd)**

## Changelog

Changes since v1.4.24:

- Add `is_banking` paarmeter to ingress flow

Changes since v1.4.23:

- Include transaction details into crypto egress flow succesfull callback

Changes since v1.4.22:

- Add Tron support for crypto ingress flow

Changes since v1.4.21:

- Update crypto related flows with checks and errors

Changes since v1.4.20:

- Update callback payload for fiat to crypto flow

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

- Changed some fields for S2S

Changes since v1.4.8:

- Updated confirmation callback

Changes since v1.4.7:

- Updated docs styling

Changes since v1.4.6:

- Updated docs styling

Changes since v1.4.5:

- Updated some fields for 3DS

Changes since v1.4.4:

- Updated docs styling

Changes since v1.4.3:

- Updated docs structure

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

# API flows

## Ingress flow

1. Merchant [creates an ingress invoice url](#create-ingress-invoice).

1. Merchant redirects user to the returned `invoice_url`.

1. **ON/RAMP** handles the payment.

1. Once the payment gets cleared, **ON/RAMP** will [call back the merchant](#callback-ingress-invoice)
   to finalize the transaction.

1. **ON/RAMP** redirects the user back to merchant.

## App Egress flow

1. Merchant [creates an egress invoice url](#create-egress-invoice-app-redirection-flow).

1. Merchant redirects user to the returned `invoice_url`.

1. **ON/RAMP** gathers required user's data and consent.
   If the payment is not feasible or was rejected by the user, it will fail at this point.

1. **ON/RAMP** [calls back the merchant](#callback-egress-invoice) returning a reference for execution.

1. Merchant accepts, rejects or postpones the egress payment.

1. If postponed in the callback, merchant will have up to 3 days to [accept or reject it](#mark-operation).

1. The payment will get automatically rejected if merchant fails to approve or reject within
   the 72 hours timeout.

## Email Egress flow

1. Merchant [creates a user email egress invoice](#create-egress-invoice-user-email-flow).

1. **ON/RAMP** prepares the payment and returns `invoice_id` reference.
   Merchant can decide whether to accept any user or only users previously registered on
   **ON/RAMP** using the `accept_unregister_user` option.
   On success, the returned `invoice_id` reference shall be used by the Merchant
   to [accept or reject](#mark-operation) the payment.

1. Merchants will have up to 3 days to accept or reject the payment, unless the option `required_merchant_confirmation`
   is set to `false`, in which case it will be always considered accepted by the merchant.

1. If the user is not already registered, **ON/RAMP** will send emails to the user requesting his/her registration.
   Users will have up to 3 days to register. If they don't, the egress payment will get cancelled **even if it was previously
   accepted by the merchant**.

# Invoice expiration

Each invoice expires after the timeout given on creation, or once it has been paid. This ensures
that the user can not accidentally pay twice for the same ingress invoice, nor get paid twice from
the same egress invoice.

As a consequence, the merchant must create a new invoice every time a user requests a payment.
If a user clicks on an expired invoice, they will get redirected back to the merchant.
