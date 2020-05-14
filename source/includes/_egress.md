# Egress API

## Create Egress Invoice (App redirection flow)


> Example Call

```shell
curl https://api.onramp.ltd/rpc/create_egress_invoice                     \
  -H "x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000"   \
  -H "Content-Type: application/json"                                     \
  -X POST -d '{ "fiat_amount"        : 3000
              , "fiat_currency"      : "EUR"
              , "payment_ack_url"    : "wwww.example.com"
              , "user_redirect_url"  : "www.example.com?user_redirected"
              , "timeout_in_sec"     : 3600
              , "offer_skin"         :
                  { "title" : "The Nice merchant"
                  , "image" : "https://static.example.com/merchant-logo"
                  , "description": "¥‎435.22 redemption from your account"
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
                  , "kyc_verified"           : "1999-11-24 09:14:49.087362+00"
                  , "kyc_document"           : "Passport"
                  , "kyc_reference"          : "25177aa2-d848-846d-226c-97ec3096f5fe"
                  , "address_verified"       : "1999-11-24 09:14:49.087362+00"
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
    , "image"      : "https://static.example.com/merchant-logo"
    , "description": "¥‎435.22 redemption from your account"
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
    , "kyc_verified"           : "1999-11-24 09:14:49.087362+00"
    , "kyc_document"           : "Passport"
    , "kyc_reference"          : "25177aa2-d848-846d-226c-97ec3096f5fe"
    , "address_verified"       : "1999-11-24 09:14:49.087362+00"
    , "address_doc_reference"  : "3sdffdss-4mgo-44gfd-34dx-r34rfdfs4gf"
    , "player_level"           : "New"
    , "member_since"           : "14:07:2019"
    , "merchant_customer_id"   : "u2340112"
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
fiat_amount       | Integer      | EUR amount to be paid denominated in cents.
fiat_currency     | String       | The constant `"EUR"`.
payment_ack_url   | String       | Merchant callback endpoint to confirm egress transaction. It should be a complete, well formed, url.
user_redirect_url | String       | Where to redirect the user after the egress has been confirmed. It should be a complete, well formed, url.
timeout_in_sec    | Integer      | When to expire the link if unused.
offer_skin        | Egress Skin  | Specify how the offer should be displayed to the user.
billing_details   | Billing Details | User billing details. (Optional)

### Egress Skin

Field             |   Type      | Description
----------------- | ----------- | ---------
title             | string      | Short string containing merchant's or redemption's name.
image             | url         | Image to stylize the offer.
description       | string      | A text explaining what the user is redeeming.

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


### Callback Egress Invoice

Once **ON/RAMP** is ready to commit the payment, it will call back merchant to confirm. **It is only at this point when the user transaction should be consider done and be debited at merchant's side**. If the merchant was able to debit the user, then it should answer the callback with an http 200 status code; if the merchant was unable to debit the user, it should answer with an http 204 status code (this will cancel the user payment, expiring his invoice link). **Any other status code** but 200 or 204 **will be treated as an internal error from the merchant side**, pausing the user payment and prompting manual intervention, potentially delaying the process several hours.

### Response JSON Fields

Field       | Type    | Description
----------- | ------- | -----------
invoice_id  | String  | Internal **ON/RAMP**'s Invoice Identifier.
invoice_url | String  | Url where to redirect user.


## Create Egress Invoice (User email flow)

> Example Call

```shell
curl https://api.onramp.ltd/rpc/send_funds_to_email                           \
  -H "x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000"       \
  -H "Content-Type: application/json"                                         \
  -X POST -d '{ "fiat_amount"        : 3000
              , "fiat_currency"      : "EUR"
              , "user_redirect_url"  : "www.example.com?user_redirected"
              , "offer_skin"         :
                  { "title" : "The Nice merchant"
                  , "image" : "https://static.example.com/merchant-logo"
                  , "description": "¥435.22 redemption from your account"
                  }
              , "onramp_user_email"  : "user@example.com"
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
                  , "kyc_verified"           : "1999-11-24 09:14:49.087362+00"
                  , "kyc_document"           : "Passport"
                  , "kyc_reference"          : "25177aa2-d848-846d-226c-97ec3096f5fe"
                  , "address_verified"       : "1999-11-24 09:14:49.087362+00"
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
, "user_redirect_url"  : "www.example.com?user_redirected"
, "offer_skin"         :     
    { "title"      : "The Nice merchant"
    , "image"      : "https://static.example.com/merchant-logo"
    , "description": "¥‎435.22 redemption from your account"
    }
, "onramp_user_email"  : "user@example.com"
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
    , "kyc_verified"           : "1999-11-24 09:14:49.087362+00"
    , "kyc_document"           : "Passport"
    , "kyc_reference"          : "25177aa2-d848-846d-226c-97ec3096f5fe"
    , "address_verified"       : "1999-11-24 09:14:49.087362+00"
    , "address_doc_reference"  : "3sdffdss-4mgo-44gfd-34dx-r34rfdfs4gf"
    , "player_level"           : "New"
    , "member_since"           : "14:07:2019"
    , "merchant_customer_id"   : "u2340112"
    }
}

```

> Response JSON

```json
{ "invoice_id" : "cde6f458-8754-4ffe-81a9-77c6d05a5540"
, "description": {}
}
```

### HTTP Request

<aside class="success"><b><code> POST /rpc/send_funds_to_email </code></b></aside>

### Request JSON Fields

Field             |   Type       | Description
----------------- | ------------ | ---------
fiat_amount       | Integer      | EUR amount to be paid denominated in cents.
fiat_currency     | String       | The constant `"EUR"`.
user_redirect_url | String       | Where to redirect the user after the egress has been confirmed. It should be a complete, well formed, url.
offer_skin        | Egress Skin  | Specify how the offer should be displayed to the user (It might not be shown in this flow).
onramp_user_email | String       | The email the user has registered with ON/RAMP.

### Egress Skin

Field             |   Type      | Description
----------------- | ----------- | ---------
title             | string      | Short string containing merchant's or redemption's name.
image             | url         | Image to stylize the offer.
description       | string      | A text explaining what the user is redeeming.
billing_details   | Billing Details | User billing details. (Optional)

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



## Approve or Reject User Email Egress Invoice (User email flow)

> Example Call

```shell
curl https://api.onramp.ltd/rpc/mark_operation                           \
  -H "x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000"  \
  -H "Content-Type: application/json"                                    \
  -X POST -d '{ "op_id"  : "cde6f458-8754-4ffe-81a9-77c6d05a5540"
              , "accept" : true
              }'
```

> Request JSON Body

```json
{ "op_id"  : "cde6f458-8754-4ffe-81a9-77c6d05a5540"
, "accept" : true
}

```

> Response JSON

```json
""
```

### HTTP Request

<aside class="success"><b><code> POST /rpc/mark_operation </code></b></aside>

### Request JSON Fields

Field             |   Type       | Description
----------------- | ------------ | ---------
op_id             | String       | UUID reference that was returned from the `send_funds_to_email` endpoint.
accept            | Boolean      | Either if the merchant wants to approve (true) or reject (false) the process.
