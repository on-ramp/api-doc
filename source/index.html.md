---
title: ON/RAMP payment API v1.0

version: 1.0

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: cURL

#toc_footers:
#  - <a href='https://coinweb.io' target='_blank' rel='noopener'>Sign Up for a Developer Key</a>

includes:
  - ingress
  - egress

search: true
---

# ON/RAMP merchant integration.


## Terminology and Acronyms

- Merchant: The service using **ON/RAMP** as payment processor.
- User: The physical person who sends or receives funds to/from the merchant through **ON/RAMP**.

## API flow

### Ingress flow

1. Merchant [creates an ingress invoice url](#create-ingress-invoice).
1. Merchant redirects user to the ingress invoice url.
1. **ON/RAMP** handles the payment.
1. If the payment succeed, **ON/RAMP** will [call back the merchant to either confirm the ingress transaction or to abort it](#callback-ingress-invoice). If
   merchants confirms, the ingress payment will be considered fulfilled, otherwise, it will be considered as failed.
1. **ON/RAMP** redirects the user back to merchant.


### Egress flow

There are currently two different supported flows for egress:

#### App redirection flow

1. Merchant [creates an egress invoice url](#create-egress-invoice-app-redirection-flow).
1. Merchant redirects user to the egress invoice url.
1. **ON/RAMP** handles the payment.
1. If the payment succeed, **ON/RAMP** will [call back the merchant to either confirm the egress transaction or to abort it](#callback-egress-invoice). If merchants confirms, the egress payment will be considered fulfilled, otherwise, it will be considered as failed.
1. User will see in **ON/RAMP** client the outcome of the process.

#### User email flow

1. Prerequisite: User will need to have an account in **ON/RAMP** with KYC verified.
1. Merchant [creates a user email egress invoice](#create-egress-invoice-user-email-flow).
1. **ON/RAMP** prepares the payment and returns a reference of it for the merchant to later approve it or reject it.
1. Before the [invoice expires](#invoice-expiration), whenever merchants want to, they can [approve or reject the process](#approve-or-reject-user-email-egress-invoice-user-email-flow). At that moment, **ON/RAMP** will finish the process, move the funds to users balance if needed and return a success or failure.
1. User will see in **ON/RAMP** client the outcome of the process.

# Calling **ON/RAMP** API endpoints.

**ON/RAMP** offers a simple REST API taking JSON values as request payload, and returning JSON values as response.
Each call shall include the headers:

- `Content-Type: application/json`

- `x-xco-authorization: Bearer AUTH_TOKEN`


```shell
curl https://api.onramp.ltd/rpc/create_ingress_invoice                          \
  -H "x-xco-authorization: Bearer $AUTH_TOKEN"                                  \
  -H "Content-Type: application/json"                                           \
  -X POST -d '{ "fiat_amount"        : 1234                                     \
              , "fiat_currency"      : "EUR"                                    \
              , "payment_ack_url"    : "wwww.example.com"                       \
              , "user_redirect_url"  : "www.example.com?user_redirected"        \
              , "timeout_in_sec"     : 3600                                     \
              , "offer_skin"         : {}                                       \
              }'                                                                \
```

Where `AUTH_TOKEN` is replaced with merchant's API key.


# Invoice expiration

Each invoice expires after the given timeout or after it has been paid once. This means the user can not accidentally paid
twice for the same ingress invoice, nor get paid twice from the same egress invoice; but it also means the merchant should
create a new invoice to the user every time the user request a new payment. If an user clicks on an expired invoice, it will
get redirected back to the merchant.
