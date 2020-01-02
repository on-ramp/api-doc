---
title: ON/RAMP payment API v0.0

version: 0.0

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

<!--
- **CIS** dynamically generate an invoice for the **USR** calling the **ON/RAMP** endpoint [Create Ingress Invoice](#create-ingress-invoice).

  - These invoices can be cached, but once paid, they will become **invalid**.

  - If cached for too long, they'd became invalid. The expiration date for each invoice is specified as an argument when creating the invoice.

  - Invoices are **ON/RAMP** url for a specific **USR**; the same invoice should not be reused among different users.

- **CIS** shows the invoice to the **USR** as a link to click and/or QR to scan.

- **ON/RAMP** process the payment. This could long anything between the instant the invoice is created to the expiration date is reached. For example, the **USR** could start the payment, stops in the middle, then resume next day.

- **ON/RAMP** [callback the **CIS**](#callback-ingress-invoice) to inform the payment had been successfully completed. This callback will include any reference included as a parameter when creating the invoice.

  - The **CIS** should return a **HTTP Status Code 200** after this callback.

  - If the **CIS** does not return a **HTTP Status Code 200** after the callback, either the callback will be retried with exponential decay until **HTTP Status Code 200** is returned, or until **some number N** of tries has been reached.

- **ON/RAMP** will inform the **USR** the payment was a success.

- **ON/RAMP** will redirect the **USR** to the url specified as a parameter when the invoice was created; or it will redirect the **USR** back to where it was if not specified. -->

### Egress flow

1. Merchant [creates an egress invoice url](#create-egress-invoice).
1. Merchant redirects user to the egress invoice url.
1. **ON/RAMP** handles the payment.
1. If the payment succeed, **ON/RAMP** will [call back the merchant to either confirm the egress transaction or to abort it](#callback-egress-invoice). If
   merchants confirms, the egress payment will be considered fulfilled, otherwise, it will be considered as failed.
1. **ON/RAMP** redirects the user back to merchant.

<!--
- The **CIS** dynamically [Create Egress Invoices](#create-egress-invoice)

  - It is possible but not required for the **CIS** to cache this invoices.

  - Each withdrawal associated with one of these invoices would be executed at most once.

- The **CIS** shows the **USR** the invoice as a URL or QR.

- After the **USR** clicks/scan it.

- **ON/RAMP** [callbacks the **CIS**](#callback-egress-invoice) to confirm the withdrawal.

- As with the ingress callback, this callback will contain any specified when the invoice was created reference.

- According to what the **CIS** returns:

  - **HTTP Status Code 200** with payload {success: true}. **ON/RAMP** considers the withdrawal confirm, and continue to send funds back to the **USR**. Then redirect the user back to where specified, or kept them on the **ON/RAMP** app if not specified.

  - **HTTP Status Code 200** with payload {success: false, redirect: <err-url\>}. **ON/RAMP** will consider the withdrawal cancel, the egress-invoice invalidated, and will redirect the **USR** to <err-url\> which could be a page where the **CIS** explains the reason it was cancel (i.e, temporally down, not enough funds \... etc).

  - Anything else: **ON/RAMP** will retry the callback with exponential decay. -->

# Calling **ON/RAMP** API endpoints.

**ON/RAMP** offers a simple REST API taking JSON values as request payload, and returning JSON values as response.
Each call shall include the headers:

- `Content-Type: application/json`

- `x-xco-authorization: Bearer 4386870c-8d10-4156-be11-2bb4635c7750`


```shell
curl https://api.onramp.ltd/rpc/create_ingress_invoice                          \
  -H "x-xco-authorization: Bearer 4386870c-8d10-4156-be11-2bb4635c7750"         \
  -H "Content-Type: application/json"                                           \
  -X POST -d '{ "fiat_amount"        : 1234                                     \
              , "fiat_currency"      : "EUR"                                    \
              , "payment_ack_url"    : "wwww.example.com"                       \
              , "user_redirect_url"  : "www.example.com?user_redirected"        \
              , "timeout_in_sec"     : 3600                                     \
              , "offer_skin"         : {}                                       \
              }'                                                                \
```

# Invoice expiration

Each invoice expires after the given timeout or after it has been paid once. This means the user can not accidentally paid
twice for the same ingress invoice, nor get paid twice from the same egress invoice; but it also means the merchant should
create a new invoice to the user every time the user request a new payment. If an user clicks on an expired invoice, it will
get redirected back to the merchant.
