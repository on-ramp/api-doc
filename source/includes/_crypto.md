# Crypto iframe integration

This part of the docs provides a brief explananation and technical steps on how to integrate the ON/RAMP **crypto iframe** in any authorized merchant site.

**IMPORTANT**: Before starting this integration, please let us know and we will add your domains to our code in order to trust them and allow the `window.postMessage` API communication between the iframe and your site work.

## Adding the iframe in a html page

In order to display the crypto iframe it is as easy as adding the following code (displayed on the right side):

> Example iframe tag

```html
<iframe
  id="crypto-iframe"
  src="https://api.onramp.ltd"
  style="width: 100%; min-width: 320px; min-height: 500px;"
></iframe>
```

As it can be seen, there are only three main attributes to deal with:

- `id`: An id to later identify the iframe. The value can be anything you want as long as it is unique. On this docs we will asume it is:

  - "crypto-iframe"

- `src`: The source of the iframe. There are two options to be used depending on the environment you are integrating with.

  - **production**: [https://api.onramp.ltd](https://api.onramp.ltd)
  - **stage**: [https://stage-api.onramp.ltd](https://stage-api.onramp.ltd)

- `style`: Styles to apply to the iframe. Here are the suggested values (This can be achieved by using a class too).

  - `width: 100%;`: This way the iframe occupies all available width from parent
  - `min-width: 320px;`: Below this mininum width, the iframe wouldn't display its contents correctly.
  - `min-height: 500px`: Although this one it is just recommended, having this minimum height will make scrollbars disappear.

## Communicating with the iframe events

The iframe just contains the code to display the information for the whole process, but in order to actually make the communications with the ON/RAMP server it relies on the host page to forward those. This is mainly because all the calls need to be authenticated and for that we are going to use the merchants API key and that can't be exposed on the frontend.

Given that, merchant host page and the iframe need a way to communicate certain events and this is achieved by using the `window.postMessage()` [API](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage).

There are several methods the iframe works with and so the merchant will have to deal with them too. Except the "Crypto Ingress Configuration", in all the other ones, the merchant should just work as a proxy and forward and return the data. The urls to be hit are the same detailed [on the first section of this doc](/#on-ramp-merchant-integration).

### Crypto Ingress Configuration

Requests for the merchant to customize the appearance and some parameters of the iframe.

- `crypto_ingress_config` (sent by the iframe)
  _No parameters_

- `crypto_ingress_config_response` (returned by the merchant)

Main Parameters:

| Field         | Type               | Description                                          | Required | Default Value |
| ------------- | ------------------ | ---------------------------------------------------- | -------- | ------------- |
| min_amount    | Integer            | Minimum fiat amount for the transaction.             | No       | 10            |
| max_amount    | Integer            | Maximum fiat amount for the transaction.             | No       | 1000          |
| fiat          | String             | ISO 3 digits fiat currency code.                     | No       | EUR           |
| x_api_key     | String             | Your X API Key to hit the quotes API                 | Yes      | --            |
| user_info     | User Info          | A JSON object containing information about the user. | Yes      | --            |
| customization | Customization Info | A JSON object containing look and feel settings.     | No       | --            |

User Info:

| Field                | Type   | Description               | Required |
| -------------------- | ------ | ------------------------- | -------- |
| merchant_customer_id | String | The merchant customer id. | Yes      |

Customization Info:

| Field            | Type   | Description                                                | Required |
| ---------------- | ------ | ---------------------------------------------------------- | -------- |
| primary_color    | String | The main accent color that will be applied on UI elements. | No       |
| font_color       | String | The font color that will be applied on texts.              | No       |
| font_color_type  | String | Either if UI is light or dark.                             | No       |
| font_family      | String | The font family name that will be applied in the whole UI. | No       |
| background_color | String | The background color for the iframe.                       | No       |

### Crypto Ingress Status

Requests for the iframe to know about the statuses of current open operations

- `crypto_op_status` (sent by the iframe)

Main Parameters:

| Field                | Type   | Description               | Required |
| -------------------- | ------ | ------------------------- | -------- |
| merchant_customer_id | String | The merchant customer id. | Yes      |

- `crypto_op_status_response` (sent by the merchant)

Main Parameters:

| Field | Type | Description                                                    | Required |
| ----- | ---- | -------------------------------------------------------------- | -------- |
| data  | JSON | Just the forwarded response from `/crypto_op_status` endpoint. | Yes      |

### Crypto Ingress Address

Requests for the iframe to ask for a crypto address to send the funds. Depending on the kind of blockchain, a different endpoint should be used:

#### FOR UTXO BASED BLOCKCHAINS (`BTC` AND `BCH`)

- `get_address` (sent by the iframe)

Main Parameters:

| Field           | Type    | Description                     | Required |
| --------------- | ------- | ------------------------------- | -------- |
| fiat_amount     | Integer | The amount of fiat to get.      | Yes      |
| fiat_currency   | String  | The currency of the fiat.       | Yes      |
| crypto_currency | String  | The crypto currency to be used. | Yes      |

- `get_address_response` (sent by the merchant)

Main Parameters:

| Field | Type | Description                                                                         | Required |
| ----- | ---- | ----------------------------------------------------------------------------------- | -------- |
| data  | JSON | Just the forwarded response from `/blockchain/api/v1/{chain}/address/new` endpoint. | Yes      |

#### FOR ACCOUNT BASED BLOCKCHAINS AND TOKENS (`ETH`, `USDT`, ...)

- `get_address_v2` (sent by the iframe)

Main Parameters:

| Field           | Type    | Description                                         | Required |
| --------------- | ------- | --------------------------------------------------- | -------- |
| fiat_amount     | Integer | The amount of fiat to get.                          | Yes      |
| fiat_currency   | String  | The currency of the fiat.                           | Yes      |
| crypto_currency | String  | The crypto currency to be used.                     | Yes      |
| token           | String  | The token of the transaction if exists (e.g. usdt). | No       |
| sender_address  | String  | The address of the sender of the transaction.       | Yes      |

- `get_address_v2_response` (sent by the merchant)

Main Parameters:

| Field | Type | Description                                                                           | Required |
| ----- | ---- | ------------------------------------------------------------------------------------- | -------- |
| data  | JSON | Just the forwarded response from a POST to `/blockchain/api/v2/transaction` endpoint. | Yes      |

- `update_address_v2` (sent by the iframe)

Main Parameters:

| Field           | Type    | Description                                                  | Required |
| --------------- | ------- | ------------------------------------------------------------ | -------- |
| fiat_amount     | Integer | The amount of fiat to get.                                   | Yes      |
| fiat_currency   | String  | The currency of the fiat.                                    | Yes      |
| crypto_currency | String  | The crypto currency to be used.                              | Yes      |
| token           | String  | The token of the transaction if exists (e.g. usdt).          | No       |
| sender_address  | String  | The address of the sender of the transaction.                | Yes      |
| xref            | String  | The xref received on the `get_address_v2_response` response. | Yes      |
| tx_hash         | String  | The hash of the transaction the user did.                    | Yes      |
| tx_amount       | Integer | The amount of the transaction the user did.                  | Yes      |

- `update_address_v2_response` (sent by the merchant)

Main Parameters:

| Field | Type | Description                                                                            | Required |
| ----- | ---- | -------------------------------------------------------------------------------------- | -------- |
| data  | JSON | Just the forwarded response from a PATCH to `/blockchain/api/v2/transaction` endpoint. | Yes      |

### Crypto Ingress Cancelation

Requests for the iframe to cancel an open operation

- `crypto_op_cancel` (sent by the iframe)

Main Parameters:

| Field | Type   | Description                          | Required |
| ----- | ------ | ------------------------------------ | -------- |
| op_id | String | The UUID of the operation to cancel. | Yes      |

- `crypto_op_cancel_response` (sent by the merchant)

Main Parameters:

| Field | Type | Description                                                   | Required |
| ----- | ---- | ------------------------------------------------------------- | -------- |
| data  | JSON | Just the forwarded response from `crypto_op_cancel` endpoint. | Yes      |

### Example of integration

On the right side, there is a full example of an implementation any merchant could use in order to integrate our iframe.

Please, note the following:

- There are some functions that are not required and they have been added to show a complete integration that should just work. The only required implementation is the one about the `window.postMessage` and `window.addEventListener` APIs.

- The example hardcodes some values and that is marked with commments. In order to make this fully functional according tou your server and API, you will need to implement or adapt calls to store the deposits and make secure connections to the ON/RAMP API using your API key.

> Example of integration

```javascript
const postMessage = (message) => {
  const stringifiedMessage = JSON.stringify(message);
  document
    .querySelector("#crypto-iframe")
    .contentWindow.postMessage(stringifiedMessage, "*");
};

const createMessage = (eventName, data, error) => {
  return {
    event: eventName,
    data,
    error,
  };
};

const postData = async (url = "", data = {}) => {
  try {
    const response = await fetch(url, {
      method: "POST", // *GET, POST, PUT, DELETE, etc.
      mode: "cors", // no-cors, *cors, same-origin
      cache: "no-cache", // *default, no-cache, reload, force-cache, only-if-cached
      credentials: "same-origin", // include, *same-origin, omit
      headers: {
        "Content-Type": "application/json",
        // 'Content-Type': 'application/x-www-form-urlencoded',
      },
      redirect: "follow", // manual, *follow, error
      referrerPolicy: "no-referrer", // no-referrer, *no-referrer-when-downgrade, origin, origin-when-cross-origin, same-origin, strict-origin, strict-origin-when-cross-origin, unsafe-url
      body: JSON.stringify(data), // body data type must match "Content-Type" header
    });
    return response.json(); // parses JSON response into native JavaScript objects
  } catch (error) {
    throw error;
  }
};

const getCryptoIngressConfig = async () => {
  // Here you should get from your server what are the config details
  // For easiness on the docs we are going to have them hardcoded here

  const minAmount = 1.01;
  const maxAmount = 5000;
  const fiatCurrency = "EUR";
  const XAPIKey = "dummy-x-api-key";
  const userInfo = {
    merchant_customer_id: 1,
    email: "roger@coinweb.io",
  };
  const customization = {
    primary_color: "rgb(80, 0, 151)",
    font_color: "#ffffff",
    font_color_type: "dark",
    font_family: "Nunito, sans-serif",
    background_color: "rgb(23, 3, 47)",
  };

  let eventName = "crypto_ingress_config_response";
  let responseData = {
    min_amount: minAmount,
    max_amount: maxAmount,
    fiat: fiatCurrency,
    x_api_key: XAPIKey,
    user_info: userInfo,
    customization: customization,
  };
  const message = createMessage(eventName, responseData);
  postMessage(message);
};

const getCryptoOpStatus = async (data) => {
  // Here you should send the provided data to your API
  // API should call the `crypto_op_status` endpoint on ON/RAMP API
  // Then the response should be sent back to the iframe
  // For easiness on the docs we are going to provide a standard implementation

  const serverURL = "<YOUR_API_URL>";
  // data contains a list of open operations
  let response = await postData(serverURL, data);

  let eventName = "crypto_op_status_response";
  const message = createMessage(eventName, response);
  postMessage(message);
};

const getAddress = async (data) => {
  // Here you should send the provided data to your API
  // API should call the `blockchain/api/v1/{chain}/address/new` endpoint on ON/RAMP API
  // Then the response should be sent back to the iframe
  // For easiness on the docs we are going to provide a standard implementation

  const serverURL = "<YOUR_API_URL>";
  // data contains a new address to be funded
  let response = await postData(serverURL, data);

  let eventName = "get_address_response";
  const message = createMessage(eventName, response);
  postMessage(message);
};

const getAddressV2 = async (data) => {
  // Here you should send the provided data to your API
  // API should call the POST to `blockchain/api/v2/transaction` endpoint on ON/RAMP API
  // Then the response should be sent back to the iframe
  // For easiness on the docs we are going to provide a standard implementation

  const serverURL = "<YOUR_API_URL>";
  // data contains a new address to be funded
  let response = await postData(serverURL, data);

  let eventName = "get_address_v2_response";
  const message = createMessage(eventName, response);
  postMessage(message);
};

const updateAddressV2 = async (data) => {
  // Here you should send the provided data to your API
  // API should call the PATCH to `blockchain/api/v2/transaction` endpoint on ON/RAMP API
  // Then the response should be sent back to the iframe
  // For easiness on the docs we are going to provide a standard implementation

  const serverURL = "<YOUR_API_URL>";
  // data contains a new address to be funded
  let response = await postData(serverURL, data);

  let eventName = "update_address_v2_response";
  const message = createMessage(eventName, response);
  postMessage(message);
};

const getCryptoOpCancel = async (data) => {
  // Here you should send the provided data to your API
  // API should call the `crypto_op_cancel` endpoint on ON/RAMP API
  // Then the response should be sent back to the iframe
  // For easiness on the docs we are going to provide a standard implementation

  const serverURL = "<YOUR_API_URL>";
  // data contains success or failure to the cancel
  let response = await postData(serverURL, data);

  let eventName = "crypto_op_cancel_response";
  const message = createMessage(eventName, response);
  postMessage(message);
};

window.addEventListener("message", async (event) => {
  const trustedDomains = [
    "https://stage-wallet.onramp.ltd",
    "https://onrampwallet.com",
  ];
  const defaultError = "default_error";
  let eventName = null;

  if (trustedDomains.find((trustedDomain) => trustedDomain === event.origin)) {
    try {
      let parsedMessage = JSON.parse(event.data);
      eventName = parsedMessage.event;

      if (eventName === "crypto_ingress_config") {
        await getCryptoIngressConfig();
      } else if (eventName === "crypto_op_status") {
        await getCryptoOpStatus(parsedMessage.data);
      } else if (eventName === "get_address") {
        await getAddress(parsedMessage.data);
      } else if (eventName === "get_address_v2") {
        await getAddressV2(parsedMessage.data);
      } else if (eventName === "update_address_v2") {
        await updateAddressV2(parsedMessage.data);
      } else if (eventName === "crypto_op_cancel") {
        await getCryptoOpCancel(parsedMessage.data);
      } else {
        throw Error({ message: "Default error" });
      }
    } catch (error) {
      const message = createMessage(
        eventName || default_error,
        undefined,
        error
      );
      postMessage(message);
    }
  }
});
```
