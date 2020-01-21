# Egress API

## Create Egress Invoice


> Example Call

```shell
curl https://api.onramp.ltd/rpc/create_egress_invoice                     \
  -H "x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000"   \
  -H "Content-Type: application/json"                                     \
  -X POST -d '{ "fiat_amount"        : 3000                               \
              , "fiat_currency"      : "EUR"                              \
              , "payment_ack_url"    : "wwww.example.com"                 \
              , "user_redirect_url"  : "www.example.com?user_redirected"  \
              , "timeout_in_sec"     : 3600                               \
              , "offer_skin"         :                                    \
                  { "title" : "The Nice merchant"                         \
                  , "image" : "https:static.example.com/merchant-logo"    \
                  , "description": "¥‎435.22 redemption from your account" \
                  }                                                       \
              }'
```

> Request JSON Body

```json
{ "fiat_amount"        : 3000
, "fiat_currency"      : "EUR"
, "payment_ack_url"    : "wwww.example.com"
, "user_redirect_url"  : "www.example.com?user_redirected"
, "timeout_in_sec"     : 3600
, "offer_skin"         :     
    { "title"      : "The Nice merchant"
    , "image"      : "https:static.example.com/merchant-logo"
    , "description": "¥‎435.22 redemption from your account"
    }
}

```

> Response JSON

```json
{ "invoice_id": "cde6f458-8754-4ffe-81a9-77c6d05a5540"
, "invoice_url": "https://wallet.onramp.ltd/egressing?reference=cde6f458-8754-4ffe-81a9-77c6d05a5540"
}
```

### HTTP Request

<aside class="success"><b><code> POST /rpc/create_egress_invoice </code></b></aside>

### Request JSON Fields

Field             |   Type       | Description
----------------- | ------------ | ---------
fiat_amount       | Integer      | Eur amount to be paid denominated in cents .
fiat_currency     | String       | The constant `"EUR"`.
payment_ack_url   | String       | Merchant callback endpoint to confirm egress transaction. It should be a complete, well formed, url.
user_redirect_url | String       | Where to redirect the user after the egress has been confirmed. It should be a complete, well formed, url.
timeout_in_sec    | Integer      | When to expire the link if unused.
offer_skin        | Egress Skin  | Specify how the offer should be displayed to the user.

### Egress Skin

Field             |   Type      | Description
----------------- | ----------- | ---------
title             | string      | Short string containing merchant's or redemption's name.
image             | url         | image to stylized the offer.
description       | string      | A text explaining what the user is redeeming.


### Callback Egress Invoice

Once **ON/RAMP** is ready to commit the payment, it will call back merchant to confirm. **It is only at this point
when the user transaction should be consider done and be debited at merchant's side**. If the merchant was able to
debit the user, then it should answer the callback with an http 200 status code; if the merchant was unable to
debit the user, it should answer with an http 204 status code (this will cancel the user payment, expiring his
invoice link). **Any other status code** but 200 or 204 **will be treated as an internal error from the merchant side**,
pausing the user payment and prompting manual intervention, potentially delaying the process several hours.

### Response JSON Fields

Field       | Type    | Description
----------- | ------- | -----------
invoice_id  | String  | Internal **ON/RAMP**'s Invoice Identifier.
invoice_url | String  | Url where to redirect user.
