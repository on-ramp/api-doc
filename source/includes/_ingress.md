# Ingress API

Any of the endpoints below will return:

- **200** http status if everything was ok.
- **400** http status if some parameters were invalid (It may specify exactly which ones.)
- **500** http status if something unexpected happened on ON/RAMP's server.

## Create Ingress Invoice

> Example Call

```shell
curl https://api.onramp.ltd/rpc/create_ingress_invoice                                    \
  -H "x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000"                   \
  -H "Content-Type: application/json"                                                     \
  -X POST -d '{ "fiat_amount"        : 3000
              , "fiat_currency"      : "EUR"
              , "payment_ack_url"    : "wwww.example.com"
              , "user_redirect_url"  : "www.example.com?user_redirected"
              , "timeout_in_sec"     : 3600
              , "offer_skin"         :
                  { "title" : "The Nice merchant"
                  , "image" : "https://static.example.com/merchant-logo"
                  , "description": "Awesome green Pluff with handler"
                  }
              , "billing_details"    :
                  { "payer_email"            : "mrpayer.payerson@email.com"
                  , "payer_first_name"       : "MrPayer"
                  , "payer_last_name"        : "Payerson"
                  , "payer_phone"            : "0034666444446"
                  , "birth_date"             : "1980-11-24 00:00:00.000000+00"
                  , "street"                 : "Main Street"
                  , "unit"                   : "Floor 7 12B"
                  , "postal_code"            : "0000001"
                  , "city"                   : "Big Town"
                  , "county"                 : "Big Town county"
                  , "state"                  : "CA"
                  , "prefecture"             : "Prefecture-ku"
                  , "country"                : "GBR"
                  , "kyc_verified"           : "2020-01-01 12:45:00.000000+00"
                  , "kyc_document"           : "Passport"
                  , "kyc_reference"          : "25177aa2-d848-846d-226c-97ec3096f5fe"
                  , "address_verified"       : "2020-01-01 12:55:00.000000+00"
                  , "address_doc_reference"  : "3sdffdss-4mgo-44gfd-34dx-r34rfdfs4gf"
                  , "player_level"           : "New"
                  , "member_since"           : "2020-01-01 12:35:00.000000+00"
                  , "merchant_customer_id"   : "u2340112"
                  }
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
    , "image"      : "https://static.example.com/merchant-logo"
    , "description": "Awesome green Pluff with handler"
    }
, "billing_details"    :
    { "payer_email"            : "mrpayer.payerson@email.com"
    , "payer_first_name"       : "MrPayer"
    , "payer_last_name"        : "Payerson"
    , "payer_phone"            : "0034666444446"
    , "birth_date"             : "1980-11-24 00:00:00.000000+00"
    , "street"                 : "Main Street"
    , "unit"                   : "Floor 7 12B"
    , "postal_code"            : "0000001"
    , "city"                   : "Big Town"
    , "county"                 : "Big Town county"
    , "state"                  : "CA"
    , "prefecture"             : "Prefecture-ku"
    , "country"                : "GBR"
    , "kyc_verified"           : "2020-01-01 12:45:00.000000+00"
    , "kyc_document"           : "Passport"
    , "kyc_reference"          : "25177aa2-d848-846d-226c-97ec3096f5fe"
    , "address_verified"       : "2020-01-01 12:55:00.000000+00"
    , "address_doc_reference"  : "3sdffdss-4mgo-44gfd-34dx-r34rfdfs4gf"
    , "player_level"           : "New"
    , "member_since"           : "2020-01-01 12:35:00.000000+00"
    , "merchant_customer_id"   : "u2340112"
    }
}

```

> Response JSON

```json
{ "invoice_id": "62b570e8-9723-4287-a5a8-e825c2ffced2"
, "invoice_url": "https://wallet.onramp.ltd/ingressing?reference=a3076265-138d-4be6-89fb-d50427adaf4e"
}
```



### HTTP Request

<aside class="success"><b><code> POST /rpc/create_ingress_invoice </code></b></aside>

### Request JSON Fields

Field             |   Type          | Description
----------------- | --------------- | -----------
fiat_amount       | Integer         | EUR amount to be paid denominated in cents.
fiat_currency     | String          | The constant `"EUR"`.
payment_ack_url   | String          | Merchant callback endpoint to confirm ingress transaction. It should be a complete, well formed, url.
user_redirect_url | String          | Where to redirect the user after a successful payment. It should be a complete, well formed, url.
timeout_in_sec    | Integer         | When to expire the link if unused.
offer_skin        | Ingress Skin    | Specify how the offer should be displayed to the user.
billing_details   | Billing Details | User billing details. Please, notice how **not** all parameters inside this json object are required except `merchant_customer_id`.

### Ingress Skin

Field             |   Type      | Description
----------------- | ----------- | -----------
title             | String      | Short string containing merchant's or product's name.
image             | Url         | Image to stylize the offer.
description       | String      | A text explaining what the user is purchasing.

### Billing Details

Field                 |   Type      | Description                                                           | Required
--------------------- | ----------- | ----------------------------------------------------------------------|---------
payer_email           | Email       | Email of the user making the payment.                                 | No
payer_first_name      | String      | First name of the user making the payment.                            | No
payer_last_name       | String      | Last name of the user making the payment.                             | No
payer_phone           | String      | Including country code, without spaces or separators.                 | No
birth_date            | Date        | Date of birth of the user making the payment.                         | No
street                | String      | Street from the billing address.                                      | No
unit                  | String      | Unit from the billing address.                                        | No
postal_code           | String      | Postal code from the billing address.                                 | No
city                  | String      | City from the billing address.                                        | No
county                | String      | County from the billing address.                                      | No
state                 | String      | State from the billing address.                                       | No
prefecture            | String      | Prefecture from the billing address.                                  | No
country               | String      | Country from the billing address. Should be a 3 letter ISO Code.      | No
kyc_verified          | Date        | Date when the user was KYC verified.                                  | No
kyc_document          | String      | Type of the document. Some examples would be Passport or National_ID. | No
kyc_reference         | String      | A unique id of the KYC verification. Something like a UUID.           | No
address_verified      | Date        | Date when the address was verified.                                   | No
address_doc_reference | String      | A unique id of the address document reference.                        | No
player_level          | String      | Your internal player level. Something like New, Normal, VIP, ...      | No
member_since          | Date        | The date since that user is a member of yours.                        | No
merchant_customer_id  | String      | The merchant customer id.                                             | Yes


### Callback Ingress Invoice

Once **ON/RAMP** is ready to commit the payment, it will call back merchant to confirm. **It is only at this point
when the user transaction should be consider done and credited at merchant's side**. If the merchant was able to
credit the user, then it should answer the callback with an http 200 status code; if the merchant was unable to
credit the user, it should answer with an http 204 status code (this will cancel the user payment, expiring his
invoice link). **Any other status code** but 200 or 204 **will be treated as an internal error from the merchant side**,
pausing the user payment and prompting manual intervention, potentially delaying the process several hours.

### Response JSON Fields

Field       | Type    | Description
----------- | ------- | -----------
invoice_id  | String  | Internal **ON/RAMP**'s Invoice Identifier.
invoice_url | String  | Url where to redirect user.
