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


## Poll Operation status

This endpoint can be used to query lifecycle status of payments by their UUID, to check
completion or query rejection reason.

> Example Call

```shell
curl https://api.onramp.ltd/rpc/op_status \
  --header 'Content-Type: application/json' \
  --header 'X-XCO-Authorization: Bearer 00000000-0000-0000-0000-000000000000' \
  -X POST -d '{"invoices_id": [
                 "eaed5195-b85c-41aa-9661-10693b65976e",
                 "0928b88d-372a-475e-8095-2f20a66cfa9f",
                 "5059dadc-4463-440b-9025-694355c0a941",
                 "73f4a88c-8591-4550-9b4d-dfa687d61a89",
                 "e59eeff7-5780-4707-b1f6-fa0a7e020ce0"]}'
```

> Request JSON Body

```json
{"invoices_id": [
  "eaed5195-b85c-41aa-9661-10693b65976e",
  "0928b88d-372a-475e-8095-2f20a66cfa9f",
  "5059dadc-4463-440b-9025-694355c0a941",
  "73f4a88c-8591-4550-9b4d-dfa687d61a89",
  "e59eeff7-5780-4707-b1f6-fa0a7e020ce0"
]}
```

> Response JSON

```json
[
    {
        "op_id": "5059dadc-4463-440b-9025-694355c0a941",
        "status": "accepted",
        "credit_card_payment_attempt": {
            "accepted": true,
            "rejected": false,
            "reject_reason": null,
            "when_submitted": "2021-03-17T11:41:00.000006+00:00",
            "card_number_masked": "409849******1447",
            "card_holder_name": "EXAMPLE REDACTED"
        }
    },
    {
        "op_id": "eaed5195-b85c-41aa-9661-10693b65976e",
        "status": "waiting_user_confirmation",
        "credit_card_payment_attempt": {
            "accepted": false,
            "rejected": true,
            "reject_reason": "seon not ok with it",
            "when_submitted": "2021-03-17T11:42:00.010659+00:00",
            "card_number_masked": "498678******1000",
            "card_holder_name": "EXAMPLE REDACTED"
        }
    }
]
```

### HTTP Request

<aside class="success"><b><code> POST /rpc/op_status </code></b></aside>

### Request JSON

Field             |   Type          | Description                     | Required
----------------- | --------------- | --------------------------------| --------
`invoices_id`     | UUID[]          | Array of payment UUID's to poll | yes

### Response JSON

Response is an array of objects in unspecified order, each object describing one payment's
information. Invalid UUID's from request are silently skipped.

Field    | Type  | Description
-------- | ----- | ----------
`op_id`  | UUID  | Payment Operation reference from request
`status` | String | Operation status enum, see below
`credit_card_payment_attempt` | Object | Description of the associated CC payment

The `status` field can assume the following values:

Enum value | Description
---------- | ---------------------
`accepted` | Operation is finished, CC funds transfer complete
`rejected` | Operation is finished, CC funds transfer not done
`queued`   | Transitional state, ON/RAMP is about to invoke Merchant callback
`waiting_merchant_confirmation` | Awaiting delayed [approval callback](#mark-operation) from Merchant
`querying_merchant_for_confirmation` | Merchant approval callback in progress, no response yet
`waiting_user_confirmation` | Awaiting User, who might be filling out CC data, passing KYC or deciding to press the final "Confirm"

CC payment object fields:

Field      | Type    | Description
---------- | ------- | -----------
`accepted` | Bool    | `true` if CC funds transfer complete
`rejected` | Bool    | `true` if CC funds transfer cancelled
`reject_reason` | String | `null` unless `rejected`, textual reason of rejection otherwise
`when_submitted` | Date | Timestamp of CC operation creation
`card_number_masked` | String | Masked CC number used in the payment
`card_holder_name`   | String | Cardholder Name used in the payment
