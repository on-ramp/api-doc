---
title: ON/RAMP payment API v1.1

version: 1.1

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: cURL

#toc_footers:
#  - <a href='https://coinweb.io' target='_blank' rel='noopener'>Sign Up for a Developer Key</a>

includes:
  - ingress
  - egress

search: true

code_clipboard: true
---

# ON/RAMP merchant integration.


## Terminology and Acronyms

- Merchant: The service using **ON/RAMP** as payment processor.
- User: The physical person who sends or receives funds to/from the merchant through **ON/RAMP**.

## Base URL

Production:
**[https://api.onramp.ltd](https://api.onramp.ltd)**

Test:
**[https://stage-api.onramp.ltd](https://stage-api.onramp.ltd)**

## Changelog

Changes since v1.0:

 * Document structure streamlined and ToC nesting reduced.
 * Typos, convoluted wording & broken internal links fixed.


# Calling **ON/RAMP** API endpoints

**ON/RAMP** offers a simple REST API taking JSON values as request payload, and returning JSON
values as response.

Every call shall include the headers:

- `Content-Type: application/json`

- `X-XCO-Authorization: Bearer AUTH_TOKEN`


```shell
curl https://api.onramp.ltd/rpc/create_ingress_invoice \
  -H "x-xco-authorization: Bearer $AUTH_TOKEN"         \
  -H "Content-Type: application/json"                  \
  -X POST -d '{ "fiat_amount"        : 1234
              , "fiat_currency"      : "EUR"
              , "payment_ack_url"    : "wwww.example.com"
              , "user_redirect_url"  : "www.example.com?user_redirected"
              , "timeout_in_sec"     : 3600
              , "offer_skin"         : {}
              }'
```

Where `AUTH_TOKEN` is replaced with merchant's API key.


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

1. If postponed in the callback, merchant will have up to 3 days to [accept or reject it](#approve-or-reject-egress-invoice).

1. The payment will get automatically rejected if merchant fails to approve or reject within
   the 72 hours timeout.


## Email Egress flow

1. Merchant [creates a user email egress invoice](#create-egress-invoice-user-email-flow).

1. **ON/RAMP** prepares the payment and returns `invoice_id` reference.
   Merchant can decide whether to accept any user or only users previously registered on
   **ON/RAMP** using the `accept_unregister_user` option.
   On success, the returned `invoice_id` reference shall be used by the Merchant
   to [accept or reject](#approve-or-reject-egress-invoice) the payment.

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
