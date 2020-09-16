# Merchant direct access and user tracking.

## Merchant direct access

For those merchants with the option enabled, once an user interacts for the first time
 (has initiated or received at least an ingress or egress) a direct-access tile will pop-up
on his app. Clicking that tile, the user will get redirected to the merchant site to
initiate a new ingress, egress or visit.


Each of the different actions (ingress, egress or visit) can be enabled or disabled on its own,
and configured to use an specific url, faceplate and text description.


## Dynamic merchant direct access.

It is possible to override a (ingress/egress/visit) action for an specific user, setting a custom
returning url, faceplate or description for that particular user. This way, it will be possible to track
each user or promote special offers to a target subset of users.

When a merchant is initiating an ingress or egress, it can add on the request JSON payload a `visit_action`,
`ingress_action` and/or `egress_action` field to override an action. When not specified, or specified as
`null`, the user's action will be overridden to the default one.


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
              , ingress_action :
                   { description:   "Click here to add more funds again to your account"
                   , faceplate_url: "https://wwww.example.com/ingress?user=12323&promo_code=welcome_back"
                   , faceplate_url: "https://static.example.com/promo-logo.png"
                   }
              , egress_action  : null
              }'
```

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
              , ingress_action :
                   { description:   "Click here to add more funds again to your account"
                   , faceplate_url: "https://wwww.example.com/ingress?user=12323&promo_code=welcome_back"
                   , faceplate_url: "https://static.example.com/promo-logo.png"
                   }
              , egress_action  : null
              }'
```

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

              , ingress_action :
                   { description:   "Click here to add more funds again to your account"
                   , faceplate_url: "https://wwww.example.com/ingress?user=12323&promo_code=welcome_back"
                   , faceplate_url: "https://static.example.com/promo-logo.png"
                   }
              , egress_action  : null
              }'
```



### HTTP Request

<aside class="success"><b><code> POST /rpc/create_ingress_invoice </code></b></aside>
<aside class="success"><b><code> POST /rpc/create_egress_invoice  </code></b></aside>
<aside class="success"><b><code> POST /rpc/send_fund              </code></b></aside>

### Request JSON Fields

Field             |   Type      | Description                                                    | Required
----------------- | ----------- | -------------------------------------------------------------- | --------
...               | ...         | ...                                                            | ...
visit_action      | TileAction  | A GUI direct access to return to the merchant                  | No
ingress_action    | TileAction  | A GUI direct access to return to the merchant to ingress       | No
egress_action     | TileAction  | A GUI direct access to return to the merchant to egress        | No
...               |             |                                                                | ...

### TileAction


Field             |   Type      | Description                                                    | Required
----------------- | ----------- | -------------------------------------------------------------- | --------
description       | String      | Short string containing merchant's or redemption's name.       | Yes
faceplate_url     | Url         | Image to stylize the offer.                                    | Yes
url               | Url         | Url the user will get redirected to after the tile is clicked  | Yes
