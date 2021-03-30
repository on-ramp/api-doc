# Iframe integration

This part of the docs provides a brief explananation and technical steps on how to integrate the ON/RAMP deposit iframe in any authorized merchant site.

## Adding the iframe in a html page

In order to display the deposit iframe it is as easy as adding the following code:

> Example iframe tag

```html
<iframe id="onramp-iframe" src="https://onrampwallet.com/embedded-ingressing" style="width: 100%; min-width: 320px; min-height: 560px;"></iframe>
```

As it can be seen, there are only three main attributes to deal with:

- `id`: An id to later identify the iframe. The value can be anything you want as long as it is unique. On this docs we will asume it is:

	- "onramp-iframe"

- `src`: The source of the iframe. There are two options to be used depending on the environment you are integrating with.
	
	- **production**: [https://onrampwallet.com/embedded-ingressing](https://onrampwallet.com/embedded-ingressing)
	- **stage**: [https://stage-wallet.onramp.ltd/embedded-ingressing](https://stage-wallet.onramp.ltd/embedded-ingressing)

- `style`: Styles to apply to the iframe. Here are the suggested values (This can be achieved by using a class too).
	
	- `width: 100%;`: This way the iframe occupies all available width from parent
	- `min-width: 320px;`: Below this mininum width, the iframe wouldn't display its contents correctly.
	- `min-height: 560px`: Although this one it is just recommended, having this minimum height will make scrollbars disappear.


## Communicating with the iframe events

The iframe contains both part of the implementation the merchant would have on their side as well as some implementation that usually happens on the wallet side.

Merchant would usually need to gather from the users what fiat amount and fiat currency they want to deposit and send it to the ON/RAMP api using the `create_ingress_invoice` endpoint to later redirect the users to the redirection url they got as a response from ON/RAMP. However, in this integration, the iframe will deal with gathering the information from the user and redirecting the user to the ON/RAMP wallet only if the payment was successful.

Given that, merchant and the iframe need a way to communicate certain events and this is achieve by using the `window.postMessage()` [API](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage).

- Events sent from merchant:
	
	- `get_deposit_details`
	- `create_ingress_invoice`

- Events sent from iframe:

	- `get_deposit_details`
	- `create_ingress_invoice`

Both of the events are going to be initiated by the iframe and the merchant will respond to them.

### Example of integration

On the right side, there is a full example of an implementation any merchant could use in order to integrate our iframe.

Please, note the following: 

- There are some functions that are not required and they have been added to show a complete integration that should just work. The only required implementation is the one about the `window.postMessage` and `window.addEventListener` APIs.

- The example hardcode some values and that is marked with commments. In order to make this fully functional according tou your server and API, you will need to implement or adapt calls to store the deposits and make secure connections to the ON/RAMP API using your API key.

> Example of integration

```javascript
const postMessage = (message) => {
	const stringifiedMessage = JSON.stringify(message)
	document.querySelector('#onramp-iframe').contentWindow.postMessage(stringifiedMessage, '*')
}

const createMessage = (eventName, data, error) => {
	return {
		event: eventName,
		data,
		error
	}
}

const postData = async (url = '', data = {}) => {
	try {
		const response = await fetch(url, {
			method: 'POST', // *GET, POST, PUT, DELETE, etc.
			mode: 'cors', // no-cors, *cors, same-origin
			cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
			credentials: 'same-origin', // include, *same-origin, omit
			headers: {
				'Content-Type': 'application/json'
				// 'Content-Type': 'application/x-www-form-urlencoded',
			},
			redirect: 'follow', // manual, *follow, error
			referrerPolicy: 'no-referrer', // no-referrer, *no-referrer-when-downgrade, origin, origin-when-cross-origin, same-origin, strict-origin, strict-origin-when-cross-origin, unsafe-url
			body: JSON.stringify(data) // body data type must match "Content-Type" header
		});
		return response.json();// parses JSON response into native JavaScript objects
	}
	catch (error) {
		throw error
	}
}

const getDepositDetails = async () => {
	// Here you should get from your server what are the deposit details
	// For easiness on the docs we are going to have them hardcoded here

	const fiatCurrency = 'EUR'
	const minFiatAmount = 10
	const maxFiatAmount = 2500

	let eventName = 'get_deposit_details'
	let responseData = {
		fiatCurrency,
		minFiatAmount,
		maxFiatAmount
	}
	const message = createMessage(eventName, responseData)
	postMessage(message)
}

const createIngressInvoice = async (data) => {
	// Here you should send the provided data to your API
	// API should call the `create_ingress_invoice` endpoint on ON/RAMP API
	// Then the response should be sent back to the iframe
	// For easiness on the docs we are going to provide a standard implementation

	const serverURL = '<YOUR_API_URL>'
	// data contains `fiat_amount`
	let response = await postData(serverURL, data)

	let eventName = 'create_ingress_invoice'
	let responseData = {
		invoiceId: response.invoice_id,
		redirectionURL: response.redirection_url
	}
	const message = createMessage(eventName, responseData)
	postMessage(message)
}

window.addEventListener('message', async (event) => {
	const trustedDomains = ['https://stage-wallet.onramp.ltd', 'https://onrampwallet.com']
	const defaultError = 'default_error'
	let eventName = null

	if (trustedDomains.find(trustedDomain => trustedDomain === event.origin)) {
		try {
			let parsedMessage = JSON.parse(event.data)
			eventName = parsedMessage.event

			if (eventName === 'get_deposit_details') {
				await getDepositDetails()
			}
			else if (eventName === 'create_ingress_invoice') {
				await createIngressInvoice(parsedMessage.data)
			}
			else {
				throw Error({message: 'Default error'})
			}
		}
		catch(error) {
			const message = createMessage(eventName || default_error, undefined, error)
			postMessage(message)
		}
	}
})
```
