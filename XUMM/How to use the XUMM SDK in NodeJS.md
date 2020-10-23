The **XUMM API** will allow developers to deliver sign requests (payloads) to app users.

A **payload** is a JSON formatted transaction template for a transaction on the XRP ledger.

XUMM app users can [scan a QR code](https://www.youtube.com/watch?v=P6hL1FDvF4c) or receive a push notification to open the sign request for a transaction. A user views the sign request and either authorizes XUMM to sign it or rejects it. If authorized, the sign request will be signed locally via XUMM, if rejected the sign request is destroyed and the transaction does not happen.

If authorized the transaction can then be sent directly to the XRP ledger. The initiating application will get realtime status updates and after signing, the end user will be returned to the initiating platform.

#### To make it simple to interact with the XUMM API, and to help build intuitive flows for end users to sign their transactions, we have released the *XUMM SDK* ([npm](https://www.npmjs.com/package/xumm-sdk)) for **Javascript**, **Typescript** and **[Deno](https://deno.land/x/xumm@deno_v0.1.2.1)** environments.

We have provided a [beginners course on setting up a code editor (Visual Studio Code) & interacting with the XRP ledger using Javascript code](https://coil.com/p/wietse/Coding-the-XRP-ledger-A-beginners-course-/kKu7G0Hhq). This is till available for viewing and instructions.

### Pre-requisite

Before beginning this XUMM SDK tutorial, we assume that you are ready to code with your code environment already set up and if required, you have followed the steps outlined in my [previous tutorial](https://coil.com/p/wietse/Coding-the-XRP-ledger-A-beginners-course-/kKu7G0Hhq).

## 1. Get your XUMM API credentials 🔑

Visit the **[XUMM Developer Console](http://apps.xumm.dev/)** by navigating to https://apps.xumm.dev. If this is your first time using the XUMM Developer Console, you will need to register an account OR sign in with your Github account, if you have one.

After logging in to the **XUMM Developer Console**, [create a new application](https://apps.xumm.dev/create-application) by clicking the `Create new application` button. Enter a title and description for your app and upload an icon or picture if desired. If you are only going to be playing and testing with the API, the app title and description do not matter.

For this tutorial entering a `Webhook URL` is not required.

#### After creating your new XUMM API application, you will be taken to a page showing your `API Key` and `API Secret`. 

Example credentials and where to find them.
[New App credentials](https://dev-to-uploads.s3.amazonaws.com/i/0ypo6flou5t29b09996t.png)

### ❗ _**Keep these credentials SAFE!**_

You can always find your **API Key** in the XUMM Developer Console however, your **API Secret** will be displayed **only once**. You can always generate a new one if required, but doing so will require updating the API Secret in all your applications.

### ❗ _**NEVER share or publish your app credentials!**_

Your **API Secret** is for backend use only. Never use these credentials in the frontend of a client side project. If you think your **API Secret** has been compromised, please regenerate a new secret in the XUMM Developer Console.

Congratulations, you have now registered your first (?) XUMM App. Let's get started!

## 2. Prepare your project and start coding 🔨

For this example we will use a Javascript (node) environment. More experienced developers will probably use Typescript or Deno, but to keep this tutorial simple we will use plain Javascript.

1. Open Visual Studio Code, or your prefrred code editor,  and create a new file - **File » New file**
2. Save your newly created file - **File » Save**. Create a new folder in a suitable project location e.g., `xumm-sdk-tutorial` and save your new file as **`index.js`**
3. **Install the XUMM SDK** in your project using the '_node package manager_'.  Open the terminal - **Terminal » New terminal** and at the prompt enter `npm install xumm-sdk`
4. **Copy then paste** the following boilerplate code into your `index.js` file:

```
javascript
const {XummSdk} = require('xumm-sdk')
const Sdk = new XummSdk('Your-API-Id', 'Your-API-Secret')

const main = async () => {
  console.log(`Hi! This is where we'll be writing some code`)
}

main()
```

**Please replace `Your-API-Id` and `Your-API-Secret` in the code with the credentials obtained from the XUMM Developer Console when you registered your app.**

What does this code actually do?

- First, we 'import' the XUMM SDK that we installed using `npm`into our code - step 3 above. We are using the `xumm-sdk` package, and we are calling it `XummSdk` in our code.
- we then initialize a new instance of the SDK, which we call `Sdk`. We will use this one later in the project as we don't interact with the XUMM SDK just yet. While not needed yet in the project the dummy API Key and API Secret need to be inserted here.
- We then define a function called `main' and we 'tell' node that this function is asynchronous (async); meaning we can run code, wait for a response, then continue running more code.
- In the function we have just created, we then tell the code to output a message to the terminal, using the `console.log` method.
- Lastly, we call our `main` function so the code will actually run.

Now let's run this code. In the terminal panel, type:
```bash
node index.js
```

This will tell `node` to run all code in your `index.js` file. You should receive the message `Hi! This is where we'll be writing some code` in your terminal and be back at the prompt awaiting furher instruction.

![Run your code :)](https://dev-to-uploads.s3.amazonaws.com/i/jz54g0zdsnarj8xfszzk.gif)

If you have your API Key and API Secret in place (see placeholder values in code above) you can check if everything is working by asking the XUMM Platform to return your registered XUMM app name.

Replace the line of code containing `console.log(...)` with a `ping` request to the XUMM platform:

```javascript
const appInfo = await Sdk.ping()
```

This line of code calls the `ping` method of the XUMM SDK. We `await` the results, as it may take a brief moment to connect to the XUMM platform and get a response. When we get a response, we are making the results accessible under the name `appInfo`.

Now let us return the application name we retrieved from the XUMM platform to the console by using `console.log` once again. When you start typing, Visual Studio Code will suggest available properties:

![Code autocomplete](https://smaviom.dlvr.cloud/pasted_1.png)

Our `main` code should now look like this:

```javascript
const main = async () => {
  const appInfo = await Sdk.ping()
  console.log(appInfo.application.name)
}
```

Let's test the code:
![Sample code](https://osliq6d.dlvr.cloud/123.gif)

It looks like we are able to reach the XUMM platform 🎉 😎

## 3. Your first payload 🔔
Now that we have established a connection to the XUMM platform using the XUMM SDK, it is time to actually send something that we can sign. We will send a "transaction template" to the XUMM platform, called a `Payload`. The lifecycle of a payload is explained [in more detail](https://xumm.readme.io/docs/payload-workflow) in the XUMM API documentation.

Some things to consider:

1. A payload (sign request) sent to the XUMM platform should be formatted as per [XRP ledger transaction specificaftions](https://xrpl.org/transaction-types.html), but some fields may be omitted as XUMM will automatically fill them in if able to do so.
2. Usually, a payload sent to the XUMM platform will be signed by the end user (e.g., for a sign in, subscription, payment, ...). For this tutorial, you will be the initiator and the end user, either signing or rejecting the payload.
3. The **first sign request** from a specific XUMM app will **always** have to be **scanned using a QR** code on the desktop **or redirected in the case of a smartphone**. Once an end user trusts your application, by signing your request, your application can obtain a user specific `user token` to deliver future sign requests using push notificaftions.
4. This tutorial is running on your own computer, from the terminal so we will need to copy then paste a sign request URL to our browser. In a real life application, a nicer flow for end users can be created with redirects / [live status updates](https://xumm.readme.io/docs/payload-status#websocket) / [push and fallback QR codes](https://xumm.readme.io/docs/pushing-sign-requests), etc.

Our first payload will be a `Payment` transaction type. A basic `Payment` payload you can send to the XUMM platform would be as per the following example:

{% gist https://gist.github.com/WietseWind/d6e20688389a02f351e3486f1f381779/04ef5c2a0f916baa5d1b5d15458e3a1d7b549150 file=simple-payment.json %}

If, like in the minimal example above, no amount is entered, the end user will be able to select the currency and amount to send in XUMM after opening the sign request. You could also add more details to your payload, as per the following example:

{% gist https://gist.github.com/WietseWind/d6e20688389a02f351e3486f1f381779/04ef5c2a0f916baa5d1b5d15458e3a1d7b549150 file=payment.json %}

This sample specifies an amount in Drops (one XRP is one million Drops, the featured **ten thousand drops** represents 0.01 XRP) and a Memo ([HEX encoded](https://gist.github.com/WietseWind/5cbcc8d15fa2be3ae7d4c20962af0a8e) string). We will use this example in our code.

_Please change the destination account to an account that you own (remember to use your destination tag if this is required), or if you wish, you can test by sending a small amount of XRP to the XRPL Labs team by using the account in the example. _

We are going to use the `Sdk.payload.create()` code to do this. We will provide **two** arguments to the `Sdk.payload.create(..., ...)` code.

The first argument will be our payload (like in our previous example above) and in the second argument we will provide a flag (boolean, `true`) telling the code to return an error, if one occurs.

Our code now looks like this:

{% gist https://gist.github.com/WietseWind/d6e20688389a02f351e3486f1f381779 file=index.js %}

Let's run the code. In the terminal panel, type the previously issued command to run our code: `node index.js`. This should get you your very first XUMM sign request 🎉

![Your first payload!](https://dev-to-uploads.s3.amazonaws.com/i/oi1etslmure3qdj0114z.gif)

## 4. Verify the results ⛑ and push 🚀
You have now sent a payload to the XUMM platform using the XUMM SDK and have now received a XUMM sign request.

You opened the `next.always` URL, scanned the QR code with your smartphone running XUMM (or from your smartphone and got redirected to the XUMM app) and you have signed your first request. Well done!

Before we move on we must first verify the payload outcome. As we discussed before, once signed, you would receive a `user token` to send your next payload using a Push notification.

#### Please note: this is where this tutorial gets more technical. Software developers will be able to follow this. If you've never coded before this will probably require some further time to learn and understand what is happening and how to achieve the desired end result.

In this part of the transaction we are not just creating a sign request by sending a payload, we are also subscribing to payload events. We will use the following methods in our code. `Sdk.payload.create()` and `Sdk.payload.createAndSubscribe()`.

The `createAndSubscribe` method accepts the payload in the first argument (just like the `create` method), but the second argument is a _function_ that gets invoked every time the XUMM platform asynchronously sends an update about the created payload.

The subscription for payload events will stay alive until something is returned by the _function_ (second argument) that is passed to the `createAndSubscribe` method. Here is an example:

{% gist https://gist.github.com/WietseWind/e8b07274192020eb65f75b98568b903f file=index.js %}

When you execute this code, you will immediately see the payload response containing the URL to open the payload. At the end of the code we console log the `subscription.created` object, containing the created payload data that we previously got directly from the `create()` method. 

The other code only executes immediately after this, as the first payload events are fired once the SDK connects to the XUMM platform.

If the event data contains a `signed` property, depending on whether the payload has been signed, causes the callback _function_ to `console.log` a message, and return either the event data (if signed) or a boolean `false` (if rejected). By returning something (anything), the subscription then ends.

**Let's test this example. Open the created payload URL on your desktop, and while the SDK is waiting for status updates, try scanning the QR code with XUMM. You should now see the `opened` event live in your terminal 😎**

When this is working successfully, we will then complete this by adding some code to wait for the `signed: true` event (signing the transaction was successful) so we can get our long awaited `user token` to deliver our future sign requests with a Push notification.

{% gist https://gist.github.com/WietseWind/4d844eb3a77bdca59cdb14c3e511d894 file=index.js %}

When we run this code, open the payload URL on our smartphone, sign it with XUMM, your terminal should look like this. Note the User Token at the end 🎉

![We now have a user token!](https://dev-to-uploads.s3.amazonaws.com/i/ndb9f5rp7s95usfay36s.png)

The user token is a unique token for **your XUMM app** in combination with **the specific XUMM user**. To use the token to deliver a sign requst per Push notification, let's adjust our first (minimal) sample payload to include the user token:

```json
{
  "TransactionType": "Payment",
  "Destination": "rwietsevLFg8XSmG3bEZzFein1g8RBqWDZ",
  "Amount": "10000"
}
```

_Please change the destination account to an account that you own (remember to use your destination tag if this is required), or if you wish, you can test by sending a small amount of XRP to the XRPL Labs team by using the account in the example. _

This payload only contains the XRP ledger specific transaction template. Because we are now going to send XUMM platform specific information as well, we need to wrap the XRP ledger transaction template in an object called `txjson`. We can then add [XUMM platform specific information](https://xumm.readme.io/reference/post-payload) to the payload as well, like `options`, `custom_meta` and the `user_token`.

Wrapping the XRP ledger transaction template in a `txjson` object, and adding the `user_token` property, our payload should now look like this:

```json
{
  "txjson": {
    "TransactionType": "Payment",
    "Destination": "rwietsevLFg8XSmG3bEZzFein1g8RBqWDZ",
    "Amount": "10000"
  },
  "user_token": "..."
}
```
_Please change the destination account to an account that you own (remember to use your destination tag if this is required), or if you wish, you can test by sending a small amount of XRP to the XRPL Labs team by using the account in the example. _

If we use the user token we received from our previously signed payload and use that in our code, you will see your sign request popping up on your smartphone. Well done!

{% gist https://gist.github.com/WietseWind/7f04966c444909649ca43332671fc6d5 file=index.js %}

Now we will run the code, keeping an eye on our smartphone:

![Sign request Push notification](https://dev-to-uploads.s3.amazonaws.com/i/mh1zt643l9g007kd12ym.gif)

When using this in applications, it would make sense to check the `pushed` property of a created payload. If `false`, the Push notification could not be sent (e.g., if permissions were revoked by the user) then your application should fall back to the QR method. If `pushed` is `true`, it makes sense to show the end user a button to show the QR code anyway if they didn't receive the push notificaftion (eg. changed devices / other device).

## 5. Security & finishing touch 🎉

If you have made it this far, you may want to actually build something (even if it's just a hobby project) using the XUMM SDK. If you do, please  **VERIFY THE PAYLOAD RESULT ON the XRP LEDGER**.

You can use the **XRPL-TxData** package, or use the `Sdk.getTransaction(txHash)` method to do this by relying on the XUMM platform to fetch the on ledger transaction outcome for you, or, for example, by using the `xrpl-txdata` package to verify 'locally'.

By using the `xrpl-txdata` package, you can connect to one of the public XRP ledger nodes and verify the transaction. To do so, add the `xrpl-txdata` package to your project by entering this in the terminal: `npm install xrpl-txdata`. You can then verify a transaction as per the [package documentation](https://www.npmjs.com/package/xrpl-txdata).

Pay special attention to the `balanceChanges` response.
**❗ There are several reasons why you need to check. For example, a payment can yield a different result (eg. lower amount sent) than requested!**

It is your responsibility to check the transaction outcome returned from the XRP ledger rather than relying on XUMM telling you that a transaction has been signed. For example if:

- The user signed successfully in XUMM, but with a key that is no longer valid for a certain account (because multisign has been configured, an account has been rekeyed, etc.)
- The user sent a Partial Payment (e.g., sending EUR to deliver XRP, while the owned amount of EUR was insufficient due to exchange rate slippage)
- The user tried to trick you into accepting a testnet payment, by signing with a funded Testnet account

Please take a look at [this sample code implementing the `xrpl-txdata` package](https://gist.github.com/WietseWind/6c6a3aefc551927ca42e73745e56a062) to verify on ledger balance changes for a signed XUMM payload.

![Verify on ledger transaction](https://lmjk5ut.dlvr.cloud/tx.gif)

## Resources 📚
If you want to learn more about the XUMM SDK, platform, documentation, etc., make sure to check out:

- The XUMM SDK [readme](https://www.npmjs.com/package/xumm-sdk)
- The XUMM SDK [source code](https://github.com/XRPL-Labs/XUMM-SDK)
- The XUMM API [documentation](https://xumm.readme.io/docs) & API [reference](https://xumm.readme.io/reference/about)
- XUMM (end user) [support docs](https://support.xumm.app/)
- In case of questions: [support@xumm.app](mailto:support@xumm.app?subject=XUMM+SDK+-+dev.to)
