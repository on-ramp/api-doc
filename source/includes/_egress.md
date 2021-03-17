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
    , "description": "¥‎435.22 redemption from your account"
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
{ "invoice_id": "cde6f458-8754-4ffe-81a9-77c6d05a5540"
, "invoice_url": "https://wallet.onramp.ltd/egressing?reference=cde6f458-8754-4ffe-81a9-77c6d05a5540"
, "invoice_url_qr": "https://api.onramp.ltd/qr_encode/68747470733a2f2f73746167652d77616c6c65742e6f6e72616d702e6c74642f696e6772657373696e673f7265666572656e63653d65646361623662342d343464342d343763652d396261322d366636656562626661313567"
}
```

### HTTP Request

<aside class="success"><b><code> POST /rpc/create_egress_invoice </code></b></aside>

### Request JSON Fields

Field             |   Type          | Description                                                                                                                          | Required
----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------ | --------
fiat_amount       | Integer         | Amount to be paid denominated in cents.                                                                                              | Yes
fiat_currency     | String          | Currency identifier following the [ISO 4217 standard](https://en.wikipedia.org/wiki/ISO_4217). Valid values are `"EUR"` or `"USD"`.  | Yes
payment_ack_url   | Url             | Merchant callback endpoint to confirm egress transaction. It should be a complete, well formed, url.                                 | Yes
user_redirect_url | Url             | Where to redirect the user after the egress has been confirmed. It should be a complete, well formed, url.                           | Yes
timeout_in_sec    | Integer         | When to expire the link if unused.                                                                                                   | Yes
offer_skin        | Egress Skin     | Specify how the offer should be displayed to the user.                                                                               | Yes
billing_details   | Billing Details | User billing details. Please, notice how **not** all parameters inside this json object are required except `merchant_customer_id`.  | Yes
cancel_callback   | Url             | Merchant callback endpoint to be called when an egress payment couldn't be completed                                                 | No

### Egress Skin

Field             |   Type      | Description                                               | Required
----------------- | ----------- | --------------------------------------------------------- | --------
title             | String      | Short string containing merchant's or redemption's name.  | Yes
image             | Url         | Image to stylize the offer.                               | Yes
description       | String      | A text explaining what the user is redeeming.             | Yes


### Billing Details

Field                 |   Type      | Description                                                           | Required
--------------------- | ----------- | --------------------------------------------------------------------- | --------
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


### Callback Egress Invoice

Once **ON/RAMP** is ready to commit the payment, it will call back merchant to confirm. **It is only at this point
when the user transaction should be consider done and be debited at merchant's side**. If the merchant was able to
debit the user, then it should answer the callback with an http 200 status code; if the merchant was unable to
debit the user, it should answer with an http 204 status code (this will cancel the user payment, expiring his
invoice link). **Any other status code** but 200 or 204 **will be treated as an internal error from the merchant side**,
pausing the user payment and prompting manual intervention, potentially delaying the process several hours.

> IMPORTANT: This callback is going to be done with **HTTP POST** method and not with GET, either for `payment_ack_url` or `cancel_url`.

### Response JSON Fields

Field          | Type    | Description
-------------- | ------- | -----------
invoice_id     | String  | Internal **ON/RAMP**'s Invoice Identifier.
invoice_url    | Url     | Url where to redirect user.
invoice_url_qr | Url     | Source Url for an image with QR-encoded `invoice_url`


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
{ "invoice_id" : "cde6f458-8754-4ffe-81a9-77c6d05a5540"
, "description": {}
, "message": ""
}
```

### HTTP Request

<aside class="success"><b><code> POST /rpc/send_funds_to_email </code></b></aside>

### Request JSON Fields

Field             |   Type          | Description                                                                                                                         | Required
----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------| --------
fiat_amount       | Integer         | Amount to be paid denominated in cents.                                                                                             | Yes
fiat_currency     | String          | Currency identifier following the [ISO 4217 standard](https://en.wikipedia.org/wiki/ISO_4217). Valid values are `"EUR"` or `"USD"`. | Yes
user_redirect_url | Url             | Where to redirect the user after the egress has been confirmed. It should be a complete, well formed, url.                          | Yes
offer_skin        | Egress Skin     | Specify how the offer should be displayed to the user (It might not be shown in this flow).                                         | Yes
billing_details   | Billing Details | User billing details. Please, notice how **not** all parameters inside this json object are required except `merchant_customer_id`. | Yes
onramp_user_email | String          | The email the user has registered with ON/RAMP.                                                                                     | Yes
cancel_callback   | Url             | Merchant callback endpoint to be called when an egress payment couldn't be completed                                                | No
accept_unregister_user | Bool       | If `false` egress to users without an **ON/RAMP** account at the moment the egress was created will be immediately rejected. Defaults to `false` | No  
required_merchant_confirmation | Bool | If `true`, **ON/RAMP** will wait for a merchant confirmation before releasing the funds. Defaults to `true`                       | No

### Egress Skin

Field             |   Type      | Description                                               | Required
----------------- | ----------- | --------------------------------------------------------- | --------
title             | String      | Short string containing merchant's or redemption's name.  | Yes
image             | Url         | image to stylize the offer.                               | Yes
description       | String      | A text explaining what the user is redeeming.             | Yes

### Billing Details

Field                 |   Type      | Description                                                           | Required
--------------------- | ----------- | --------------------------------------------------------------------- | --------
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


### Response JSON Fields

Field       | Type    | Description
----------- | ------- | -----------
invoice_id  | String  | Internal **ON/RAMP**'s Invoice Identifier.
description | String  | Description about the invoice. Might be empty.
message     | String  | **MIGHT NOT BE PRESENT**: Additional status if user is not registered.



**Note**: If `accept_unregister_user` is set to `false` and user inputs a not registered email, **ON/RAMP** will respond with the attached response.

> Response JSON

```json
{
    "hint": null,
    "details": null,
    "code": "00001",
    "message": "ERR_CODE_UNKNOWN_USER_ACCOUNT"
}
```

In that case, merchant should inform the user about email account being invalid and show a link to sign up with **ON/RAMP** at [https://onrampwallet.com](https://onrampwallet.com).

