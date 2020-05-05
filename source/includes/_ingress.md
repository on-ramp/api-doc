# Ingress API

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
                  , "image" : "https:static.example.com/merchant-logo"
                  , "description": "Awesome green Pluff with handler"
                  }
              , "billing_details"    :
                  { "payer_email"            : "mrpayer.payerson@email.com"
                  , "payer_first_name"       : "MrPayer"
                  , "payer_last_name"        : "Payerson"
                  , "payer_phone"            : "0034666444446"
                  , "birth_date"             : "24:11:1980"
                  , "street"                 : "Main Street"
                  , "unit"                   : "Floor 7 12B"
                  , "postal_code"            : "0000001"
                  , "city"                   : "Big Town"
                  , "county"                 : "Big Town county"
                  , "state"                  : "CA"
                  , "prefecture"             : "Prefecture-ku"
                  , "country"                : "GBR"
                  , "kyc_verified"           : "24:11:1999"
                  , "kyc_document"           : "Passport"
                  , "kyc_reference"          : "25177aa2-d848-846d-226c-97ec3096f5fe"
                  , "address_verified"       : "24:11:1999"
                  , "address_doc_reference"  : "3sdffdss-4mgo-44gfd-34dx-r34rfdfs4gf"
                  , "player_level"           : "New"
                  , "member_since"           : "14:07:2019"
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
    , "image"      : "https:static.example.com/merchant-logo"
    , "description": "Awesome green Pluff with handler"
    }
, "billing_details"    :
    { "payer_email"            : "mrpayer.payerson@email.com"
    , "payer_first_name"       : "MrPayer"
    , "payer_last_name"        : "Payerson"
    , "payer_phone"            : "0034666444446"
    , "birth_date"             : "24:11:1980"
    , "street"                 : "Main Street"
    , "unit"                   : "Floor 7 12B"
    , "postal_code"            : "0000001"
    , "city"                   : "Big Town"
    , "county"                 : "Big Town county"
    , "state"                  : "CA"
    , "prefecture"             : "Prefecture-ku"
    , "country"                : "GBR"
    , "kyc_verified"           : "24:11:1999"
    , "kyc_document"           : "Passport"
    , "kyc_reference"          : "25177aa2-d848-846d-226c-97ec3096f5fe"
    , "address_verified"       : "24:11:1999"
    , "address_doc_reference"  : "3sdffdss-4mgo-44gfd-34dx-r34rfdfs4gf"
    , "player_level"           : "New"
    , "member_since"           : "14:07:2019"
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
----------------- | --------------- | ---------
fiat_amount       | Integer         | Eur amount to be paid denominated in cents .
fiat_currency     | String          | The constant `"EUR"`.
payment_ack_url   | String          | Merchant callback endpoint to confirm ingress transaction. It should be a complete, well formed, url.
user_redirect_url | String          | Where to redirect the user after a successful payment. It should be a complete, well formed, url.
timeout_in_sec    | Integer         | When to expire the link if unused.
offer_skin        | Ingress Skin    | Specify how the offer should be displayed to the user.
billing_details   | Billing Details | User billing details. (Optional)

### Ingress Skin

Field             |   Type      | Description
----------------- | ----------- | ---------
title             | String      | Short string containing merchant's or product's name.
image             | Url         | image to stylized the offer.
description       | String      | A text explaining what the user is purchasing.

### Billing Details

Field                 |   Type      | Description
--------------------- | ----------- | ---------
payer_email           | Email       | Email of the user making the payment.
payer_first_name      | String      | First name of the user making the payment.
payer_last_name       | String      | Last name of the user making the payment.
payer_phone           | String      | Including country code, without spaces or separators.
birth_date            | Date        | Date of birth of the user making the payment.
street                | String      | Street from the billing address.
unit                  | String      | Unit from the billing address.
postal_code           | String      | Postal code from the billing address.
city                  | String      | City from the billing address.
county                | String      | County from the billing address.
state                 | String      | State from the billing address.
prefecture            | String      | Prefecture from the billing address.
country               | String      | Country from the billing address. Should be a 3 letter ISO Code.
kyc_verified          | Date        | Date when the user was KYC verified.
kyc_document          | String      | Type of the document. Some examples would be Passport or National_ID.
kyc_reference         | String      | A unique id of the KYC verification. Something like a UUID.
address_verified      | Date        | Date when the address was verified.
address_doc_reference | String      | A unique id of the address document reference.
player_level          | String      | Your internal player level. Something like New, Normal or VIP would be examples of it.
member_since          | Date        | The date since that user is a member of yours.
merchant_customer_id  | String      | The merchant customer id.


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
