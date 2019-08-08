---
title: ON/RAMP payment API v1.0 

version: 1.0

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: cURL

toc_footers:
  - <a href='https://coinweb.io' target='_blank' rel='noopener'>Sign Up for a Developer Key</a>

includes:
  - ingress
  - egress
  - errors

search: true
---

# ON/RAMP payment API

In this documentation it is described how the integration with **ON/RAMP** payment System should be done.
In this first section there is a general description of the Flow between an External System that wants to integrate with **ON/RAMP**'s API** 

## Terminology and Acronyms 

- Client Service (**CIS**): The service using **ON/RAMP** as payment processor
- User (**USR**): CIS's users.

## Suggested flow

### Ingress flow

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

- **ON/RAMP** will redirect the **USR** to the url specified as a parameter when the invoice was created; or it will redirect the **USR** back to where it was if not specified.

### Egress flow

**Analogous to the ingress flow**

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

  - Anything else: **ON/RAMP** will retry the callback with exponential decay.

## Questions

- What metadata will be offer to enhance the **USR** info about the payment? Such images, text description, links to click, etc...

- Extra requirement for us? Such queries about **USR**s, due balances or payment statuses?

- Allow **USR**s to ingress offline? (Meaning doing so directly through **ON/RAMP**, without the user interacting with the **CIS**).

- Allow **USR**s to egress offline?

## Suggested offline extension for ingress/egress

### Offline Ingress flow

- When **CIS** creates an online ingress-invoice, it will add the following parameters:

  - refresh\_invoice\_url: a callback to **CIS** with any require reference. If specified and not null, once the invoice is no longer valid (the **USR** already used it or it was timeout) **ON/RAMP** will call it to generate a new invoice.The expected return payload for this callback is the same as the payload the **CIS** would use to create a new invoice, and the result would be the same.

  - invoice\_display\_key: if set to null or not specified, the ingress-invoice won't be display on the **USR** **ON/RAMP** app. If set to something else, the **ON/RAMP** will display the invoice, allowing the user to click on it at any time to initiate an ingress without directly interacting with **CIS**. If the **ON/RAMP** app gets 2 invoices from the same **CIS** with the same invoice\_display\_key, the first one will be discarded (this is a mechanism such **CIS** is able to update the invoice, such changing its description**.

### Offline Egress flow

Similar to offline ingress, we'll add the parameters refresh\_invoice\_url and invoice\_display\_key to create\_egress\_invoice.

# API Reference

This API is built in REST API style. In this sense it has resource-oriented URLs, accepts [JSON](http://www.json.org/) request bodies, returns [JSON-encoded](http://www.json.org/) responses, and uses standard HTTP response codes, authentication, and verbs.

- [`POST /v1/ingress/invoice`](#create-ingress-invoice)
- [`GET /v1/ingress/invoice/:id`](#query-ingress-invoice)
- [`POST /v1/egress/invoice`](#create-egress-invoice)
- [`GET /v1/egress/invoice/:id`](#query-egress-invoice)


# Base URL

**(TO BE DEFINED - Example)**

**https://api.onramp.com**

# Client Caching

**Every time** a client request either [Create Ingress Invoice](#create-ingress-invoice) or [Create Egress Invoice](#create-egress-invoice**, a completely new invoice will be generated by **ON/RAMP**. In that sense it is not recommended to cache this calls if the consumer wants to create a new Invoice.

# Authentication

> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl "https://api.onramp.io/api/v1/ingress/invoice" -H "Authorization: Bearer AUTH_TOKEN"
```

> Make sure to replace `AUTH_TOKEN` with your API key.

**ON/RAMP** uses API keys to allow access to the API. You can register a new API key at our [developer portal](https://onramp.com/developers).

**ON/RAMP** expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: Bearer AUTH_TOKEN`

<aside class="notice">
You must replace <code>AUTH_TOKEN</code> with your personal API key.
</aside>

