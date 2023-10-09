# Server to Server (S2S)

**Note:** This API is restricted and you should only use it with previous authorization.

## Flow

1. Merchant makes a request to `/s2s/create_operation` endpoint.
2. Merchant gets `op_id` in the response and stores it for further usage if needed.
3. `callback_url` starts receiving statuses.
4. When merchant receives `WAITING_USER_TO_BE_REDIRECTED`, they redirect the user to the provided url.
5. User follows the url, completes 3DS and gets redirected to `user_redirect_url`.
6. Steps 4 and 5 might be repeated one additional time if the acquirer deems it necessary.
7. Once the merchant receives a status that is `ACCEPTED` or `REJECTED`, the process finishes.

### Status overview

Here is the list of all the existing statuses and their definitions.

- `PENDING`:
  Operation has just been created.
- `WAITING_USER_TO_BE_REDIRECTED`:
  3DS url has been created and user should be redirected to that.
- `USER_REDIRECTED`:
  User has been redirected to the 3DS page.
- `PROCESSING_USER_PAYMENT`:
  User has completed 3DS and the payment is being processed.
- `ACCEPTED`:
  The payment has been accepted.
- `REJECTED`:
  The payment has been rejected by the bank.
- `CANCELLED`:
  Operation has been cancelled.

### Flow diagram

![](../images/s2s_flow.svg)

## Authentication

> Header

```json
"x-xco-authorization": "Bearer <API_KEY>"
```

Each API Key is associated with a specific MID.

Operations created under a specific API key will only be visible with that API key.

## Creating an operation

<aside class="success"><b><code>POST /s2s/create_operation</code></b></aside>

> Example Call

```shell
curl -X POST 'https://api.onramp.ltd/s2s/create_operation' \
-H 'Content-Type: application/json' \
-H 'x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000' \
-d '{
    "op_id": "00112233-4455-6677-8899-aabbccddeeff",
    "fiat_amount": "1000",
    "fiat_currency": "EUR",
    "callback_url": "https://api.example.com/deposit-webhook",
    "user_redirect_url": "https://example.com/user/deposits",
    "customer_id": "ffeeddcc-bbaa-9988-7766-554433221100",
    "session_id": "00112233-4455-6677-8899-aabbccddeeff",
    "first_time": false,
    "card_details": {
        "card_number": "4242424242424242",
        "card_expiry_month": "12",
        "card_expiry_year": "21",
        "card_cvv": "356",
        "card_holder_name": "John Doe",
        "email": "example@example.com",
        "first_name": "John",
        "last_name": "Doe",
        "country": "GBR"
    }
}'
```

### Request

<aside class="notice">
  Fields marked <b>For 3DSv2</b> need to <b>all</b> be present if you
  wish for a 3DSv2 transaction to occur.</aside>

<table>
  <tr>
    <th colspan=2>Field</th>
    <th colspan=2>Description</th>
    <th colspan=2>Type</th>
    <th colspan=2>Required</th>
  </tr>
  <tr>
    <td colspan=2>op_id</td>
    <td colspan=2>Unique ID to identify the operation. If not provided, it will be automatically generated</td>
    <td colspan=2>UUID</td>
    <td colspan=2>Optional</td>
  </tr>
  <tr>
    <td colspan=2>fiat_amount</td>
    <td colspan=2>Transaction amount, in <a href="https://en.wikipedia.org/wiki/ISO_4217#Minor_units_of_currency">minor unit</a> form</td>
    <td colspan=2>Positive integer</td>
    <td colspan=2>Always</td>
  </tr>
  <tr>
    <td colspan=2>fiat_currency</td>
    <td colspan=2>Transaction currency</td>
    <td colspan=2><a href="https://en.wikipedia.org/wiki/ISO_4217#Active_codes">ISO 4217</a> alpha code</td>
    <td colspan=2>Always</td>
  </tr>
  <tr>
    <td colspan=2>callback_url</td>
    <td colspan=2>URL we will return status updates to</td>
    <td colspan=2>HTTP URL</td>
    <td colspan=2>Always</td>
  </tr>
  <tr>
    <td colspan=2>user_redirect_url</td>
    <td colspan=2>URL the user will be redirected to after completing 3DS</td>
    <td colspan=2>HTTP URL</td>
    <td colspan=2>Always</td>
  </tr>
  <tr>
    <td colspan=2>customer_id</td>
    <td colspan=2>User identifier</td>
    <td colspan=2>String</td>
    <td colspan=2>Always</td>
  </tr>
  <tr>
    <td colspan=2>session_id</td>
    <td colspan=2>Unique ID of users browsing session</td>
    <td colspan=2>String</td>
    <td colspan=2>Always</td>
  </tr>
  <tr>
    <td colspan=2>first_time</td>
    <td colspan=2>Whether the invoice is for a first time deposit.</td>
    <td colspan=2>Boolean</td>
    <td colspan=2>No</td>
  </tr>
  <tr>
    <td colspan=2>card_details</td>
    <td colspan=2></td>
    <td colspan=2>Object</td>
    <td colspan=2></td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">card_details.</span>card_number</td>
    <td></td>
    <td><a href="https://en.wikipedia.org/wiki/Payment_card_number">Valid</a> credit card number</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>Always <b>(Not needed for bank payments)</b></td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">card_details.</span>card_expiry_month</td>
    <td></td>
    <td>Credit card expiry month</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>Always <b>(Not needed for bank payments)</b></td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">card_details.</span>card_expiry_year</td>
    <td></td>
    <td>Last two digits of the credit card expiry year</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>Always <b>(Not needed for bank payments)</b></td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">card_details.</span>card_cvv</td>
    <td></td>
    <td>Card CVV</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>Always <b>(Not needed for bank payments)</b></td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">card_details.</span>card_holder_name</td>
    <td></td>
    <td>Card holder's name as it appears on the card</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>Always <b>(Not needed for bank payments)</b></td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">card_details.</span>first_name</td>
    <td></td>
    <td>Users first name</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>Always</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">card_details.</span>last_name</td>
    <td></td>
    <td>Users last name</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>Always</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">card_details.</span>email</td>
    <td></td>
    <td>Users email</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>Always</td>
  </tr>
  <tr>
    <td></td>
    <td><span style="color:gray">card_details.</span>phone_number</td>
    <td></td>
    <td>Users phone number</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>Optional</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">card_details.</span>street</td>
    <td></td>
    <td>Billing address street</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>Always</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">card_details.</span>city</td>
    <td></td>
    <td>Billing address city</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>Always</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">card_details.</span>postal_code</td>
    <td></td>
    <td>Billing address postcode</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>Always</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">card_details.</span>state</td>
    <td></td>
    <td>Billing address state</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>Always</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">card_details.</span>country</td>
    <td></td>
    <td>Billing address country</td>
    <td></td>
    <td><a href="https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3">ISO 3166</a> alpha-3 code</td>
    <td></td>
    <td>Always</td>
  </tr>
  <tr>
    <td colspan=2>ip</td>
    <td colspan=2>Users IP address</td>
    <td colspan=2><a href="https://en.wikipedia.org/wiki/IP_address#IPv4_addresses">IPv4</a> address</td>
    <td colspan=2>Always</td>
  </tr>
  <tr>
    <td colspan=2>three_ds_data</td>
    <td colspan=2>Users session-specific browser data</td>
    <td colspan=2>Object
    <td colspan=2>For 3DSv2</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">three_ds_data.</span>accept_header</td>
    <td></td>
    <td>Accept header from users browser</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>For 3DSv2</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">three_ds_data.</span>challenge_window</td>
    <td></td>
    <td>Size of the authentication iframe presented to the user.
        <br>Acceptable values are:
        <br>&emsp;1 – 250 x 400
        <br>&emsp;2 – 390 x 400
        <br>&emsp;3 – 500 x 600
        <br>&emsp;4 – 600 x 400
        <br>&emsp;5 – Full screen
        <br>The default varies based on the acquirer.
    </td>
    <td></td>
    <td>Integer</td>
    <td></td>
    <td>For 3DSv2</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">three_ds_data.</span>java_enabled</td>
    <td></td>
    <td>Whether users browser can execute Java</td>
    <td></td>
    <td>Boolean</td>
    <td></td>
    <td>For 3DSv2</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">three_ds_data.</span>javascript_enabled</td>
    <td></td>
    <td>Whether users browser can execute JavaScript</td>
    <td></td>
    <td>Boolean</td>
    <td></td>
    <td>For 3DSv2</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">three_ds_data.</span>language</td>
    <td></td>
    <td>Users browser language</td>
    <td></td>
    <td><a href="https://en.wikipedia.org/wiki/IETF_language_tag">Language tag</a></td>
    <td></td>
    <td>For 3DSv2</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">three_ds_data.</span>os</td>
    <td></td>
    <td>Users operating system</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>For 3DSv2</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">three_ds_data.</span>screen_color_depth</td>
    <td></td>
    <td>Users screen bit depth of the color palette
        <br>Accepted values are 1, 4, 8, 15, 16, 24, 32 and 48.
    </td>
    <td></td>
    <td>Integer</td>
    <td></td>
    <td>For 3DSv2</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">three_ds_data.</span>screen_height</td>
    <td></td>
    <td>Users screen height in pixels</td>
    <td></td>
    <td>Integer</td>
    <td></td>
    <td>For 3DSv2</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">three_ds_data.</span>screen_width</td>
    <td></td>
    <td>Users screen width in pixels</td>
    <td></td>
    <td>Integer</td>
    <td></td>
    <td>For 3DSv2</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">three_ds_data.</span>timezone</td>
    <td></td>
    <td>Difference between UTC and users local time <b>in minutes</b>.
        <br>This will be negative if users local time is ahead of UTC.</td>
    <td></td>
    <td>Integer</td>
    <td></td>
    <td>For 3DSv2</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">three_ds_data.</span>user_agent</td>
    <td></td>
    <td>UserAgent header from users browser</td>
    <td></td>
    <td>String</td>
    <td></td>
    <td>For 3DSv2</td>
  </tr>
</table>

### Response

<aside class="success">HTTP 200</aside>

> Response Json Example

```json
[{ "op_id": "7ffb7577-e4f9-4980-89d0-a1936f9088f4" }]
```

| Field | Description                            | Type |
| ----- | -------------------------------------- | ---- |
| op_id | Operation ID to be used as a reference | UUID |

## Callbacks

<aside class="notice">Callbacks are sent to <code>callback_url</code> provided in the <code>POST /s2s/create_operation</code> call</aside>

Whenever there's a change on a operation status, a status change callback will be triggered.

Status change callbacks will be retried with exponential decay until they get answered.

**WARNING**: in rare cases, it might be possible for status change callbacks to be duplicated or received
out of order, therefore we recommend checking the `date` field to detect these anomalies.

### Request

> Callback example

```json
{
  "op_id": "c6cce9a4-af6d-4572-a0ab-d7bc2ad159ba",
  "date": 1626971748,
  "status": {
    "waiting_user_to_be_redirected": {
      "url": "https://api.onramp.ltd/s2s/deposit"
    }
  }
}
```

<table>
  <tr>
    <th>Field</th>
    <th>Description</th>
    <th>Type</th>
  </tr>
  <tr>
    <td>op_id</td>
    <td>Operation this status change refers to</td>
    <td>UUID</td>
  </tr>
  <tr>
    <td>date</td>
    <td>Status creation timestamp</td>
    <td>Unix time</td>
  </tr>
  <tr>
    <td>status</td>
    <td></td>
    <td>Object</td>
  </tr>
</table>

### Status field (one of the following)

<table>
  <tr>
    <th colspan=2>Field</th>
    <th colspan=2>Description</th>
    <th colspan=2>Type</th>
  </tr>
  <tr style="border-bottom:0.5px dashed gray">
    <td colspan=2>pending</td>
    <td colspan=2>Initial state</td>
    <td colspan=2>Object</td>
  </tr>
  </tr>
  <tr>
    <td colspan=2>waiting_user_to_be_redirected</td>
    <td colspan=2>Waiting for merchant to redirect the user</td>
    <td colspan=2>Object</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">waiting_user_to_be_redirected.</span>number</td>
    <td></td>
    <td>Increments every time a new redirection is requested. Starts at 1.</td>
    <td></td>
    <td>HTTP URL</td>
  </tr>
  <tr></tr>
  <tr style="border-bottom:0.5px dashed gray">
    <td></td>
    <td><span style="color:gray">waiting_user_to_be_redirected.</span>url</td>
    <td></td>
    <td>URL to redirect the user to</td>
    <td></td>
    <td>HTTP URL</td>
  </tr>
  <tr style="border-bottom:0.5px dashed gray">
    <td colspan=2>user_redirected</td>
    <td colspan=2>Waiting for the user to complete 3DS</td>
    <td colspan=2>Object</td>
  </tr>
  <tr style="border-bottom:0.5px dashed gray">
    <td colspan=2>processing_user_payment</td>
    <td colspan=2>Processing the payment</td>
    <td colspan=2>Object</td>
  </tr>
  <tr style="border-bottom:0.5px dashed gray">
    <td colspan=2>accepted</td>
    <td colspan=2>Operation completed</td>
    <td colspan=2>Object</td>
  </tr>
  <tr>
    <td colspan=2>rejected</td>
    <td colspan=2>Operation was rejected by the bank/acquirer</td>
    <td colspan=2>Object</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">rejected.</span>error_code</td>
    <td></td>
    <td>Bank/acquirer error identifier</td>
    <td></td>
    <td>String</td>
  </tr>
  <tr></tr>
  <tr style="border-bottom:0.5px dashed gray">
    <td></td>
    <td><span style="color:gray">rejected.</span>error_reason</td>
    <td></td>
    <td>Common error reason.
        <br>One of the following:
          <br>&ensp;<code>DO_NOT_HONOR</code>
          <br>&ensp;<code>3DS_DECLINED</code>
          <br>&ensp;<code>EXPIRED_CARD</code>
          <br>&ensp;<code>TRANSACTION_NOT_PERMITTED_TO_CARDHOLDER</code>
          <br>&ensp;<code>ISSUER_OFFLINE</code>
          <br>&ensp;<code>UNSUPPORTED_CARD_COUNTRY</code>
          <br>&ensp;<code>INSUFFICIENT_FUNDS</code>
          <br>&ensp;<code>UNSUPPORTED_CARD_TYPE</code>
          <br>&ensp;<code>UNSUPPORTED_CURRENCY</code>
          <br>&ensp;<code>INVALID_CARDNUMBER</code>
          <br>&ensp;<code>CVV2_FAILURE</code>
          <br>&ensp;<code>UNSPECIFIED_CC_ERR</code>
    </td>
    <td></td>
    <td>String</td>
  <tr>
    <td colspan=2>cancelled</td>
    <td colspan=2>Operation was cancelled on our side</td>
    <td colspan=2>Object</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">cancelled.</span>reason</td>
    <td></td>
    <td>Action that caused the operation cancellation.
        <br>One of the following:
          <br>&ensp;<code>MANUAL_CANCELLATION</code>
    </td>
    <td></td>
    <td>String</td>
  </tr>
  <tr>
    <td colspan=2>received</td>
    <td colspan=2>Funds have been transferred successfully</td>
    <td colspan=2>Object</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">received.</span>data</td>
    <td></td>
    <td>Bank-specific data. May be null.</td>
    <td></td>
    <td>Object</td>
  </tr>
  <tr>
    <td colspan=2>not_received</td>
    <td colspan=2>Funds could not be transferred</td>
    <td colspan=2>Object</td>
  </tr>
  <tr></tr>
  <tr>
    <td></td>
    <td><span style="color:gray">not_received.</span>data</td>
    <td></td>
    <td>Bank-specific data. May be null.</td>
    <td></td>
    <td>Object</td>
  </tr>

</table>

### Response

<aside class="success">HTTP 200</aside>

> Response Json Example

```json
{ "action": { "request_attempt_cancel": {} } }
```

> Note: If an unknown status is sent, merchant should respond with `continue`

```json
{ "action": { "continue": {} } }
```

| Field  | Description | Type   |
| ------ | ----------- | ------ |
| action |             | Object |

### Action (one of the following)

<table>
  <tr>
    <th>Field</th>
    <th>Description</th>
    <th>Type</th>
  </tr>
  <tr style="border-bottom:0.5px dashed gray">
    <td>request_attempt_cancel</td>
    <td>Attempt to cancel the operation.
        This is not guaranteed to succeed.</td>
    <td>Object</td>
  </tr>
  <tr>
    <td>continue</td>
    <td>Simply acknowledge the callback has been received.</td>
    <td>Object</td>
  </tr>
</table>

## Retrieving operation statuses

<aside class="success"><b><code>GET /s2s/operation_status?op_id={op_id}</code></b></aside>

This endpoint can be used to query the status of an operation.

It returns the list of status changes related to the given event.

> Example Call

```shell
curl 'https://api.onramp.ltd/s2s/operation_status?op_id=7ffb7577-e4f9-4980-89d0-a1936f9088f4' \
-H 'x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000'
```

### Request

<aside class="notice">Empty body</aside>

### Response

<aside class="success">HTTP 200</aside>

Returns a list of [callbacks](#callbacks).

## Updating an operation

<aside class="success"><b><code>POST /s2s/update_operation</code></b></aside>

Send an action to a given operation.

> Example Call

```shell
curl -X POST 'https://api.onramp.ltd/s2s/update_operation' \
-H 'Content-Type: application/json' \
-H 'x-xco-authorization: Bearer 00000000-0000-0000-0000-000000000000' \
-d '{
    "op_id": "5b99dfb2-ef07-4fb7-9d20-62d63ce0f737",
    "action": {
        "request_attempt_cancel": {}
    }
}'
```

### Request

| Field  | Description                                                                | Type   |
| ------ | -------------------------------------------------------------------------- | ------ |
| op_id  | ID of the operation to be updated.                                         | UUID   |
| action | Same action as in the response part of the [callbacks section](#callbacks) | Object |

### Response

<aside class="success">HTTP 200</aside>

<aside class="notice">Empty body</aside>

## Testing

On the testing environment several CVVs behave differently:

&emsp;`001` — payment is immediately accepted;

&emsp;`002` — payment goes through mocked 3DSv1 detached from any payment system;

&emsp;`003` — payment is immediately rejected.

&emsp;`004` — payment goes through mocked 3DSv2 detached from any payment system;

<aside class="notice">These test cases jump certain status change callbacks, notably <code>pending</code> and <code>processing_user_payment</code>, but they will still be visible on <b><code>GET /s2s/operation_status</code></b></aside>

## Bank payments flow

As a clarification, our ingress flow currently supports both paying with credit card and with our bank payments solution.

**In order to be able to use that, you need to get a specific API key from us.**

For how the flow works or what parameters are needed to be used, you can see above on the technical specifications how:

- Flow is exactly the same as in a 3DS transaction, but instead the user seeing an OTP screen to confirm the transaction, they will see a page where they will be able to choose from the available banks and continue the process.
- The parameters required are exactly the same as on the credit card payment, but except for the credit card data since this is a bank payment flow. This means the same current implementation you may have for credit card payment will work if you just remove the credit card parameters (`card_number, card_expiry_month, card_expiry_year, card_cvv, card_holder_name`).

## API changes & backwards compatibility:

To keep flexibility for future new features without losing backward compatibility, we ask you:

- To assume that any JSON could contain extra fields not specified within this document;

- To answer unknown types of status changes with `continue`.
