# Payment lifecycle API

## Mark Operation

This call is for active (dis-)approval of payments by Merchant. See [API flows](#api-flows).

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
null
```

### HTTP Request

<aside class="success"><b><code> POST /rpc/mark_operation </code></b></aside>

### Request JSON Fields

Field             |   Type       | Description
----------------- | ------------ | ---------
op_id             | String       | UUID reference that was returned from the `send_funds_to_email` endpoint.
accept            | Boolean      | Either if the merchant wants to approve (true) or reject (false) the process.
