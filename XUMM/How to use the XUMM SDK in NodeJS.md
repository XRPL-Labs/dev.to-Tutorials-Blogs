Preview at [dev.to](https://dev.to/wietse/how-to-use-the-xumm-sdk-in-node-js-5380?preview=78d28d8539a98cc4e6a6769d300b81897bf9b964f4374aca60ddf307b3d64ab790027e3958b0503551e3adba45ab898e057c304b0847ea2f89c87efd)

### Please note: this content will be split up:
Every chapter a separate tutorial referring to the previous and next post.

---

The XUMM API allows developers to deliver sign requests (payloads) to app users.

A payload is a JSON formatted transaction template for a transaction on the XRP ledger. XUMM app users can [scan a QR code](https://www.youtube.com/watch?v=P6hL1FDvF4c) or receive a push notification to open the sign request. A user views & rejects or signs, after which the transaction will be signed locally.

The transaction can then be automatically sent to the XRP ledger. The initiating application will get realtime status updates, and after signing the end user can return to the initiating platform.

#### To make it really easy to interact with the XUMM API & build intuitive flows for end users to sign their transactions, we just released the *XUMM SDK* ([npm](https://www.npmjs.com/package/xumm-sdk)) for **Javascript**, **Typescript** and **[Deno](https://deno.land/x/xumm@deno_v0.1.2.1)** environments.

I published a [beginners course on setting up a code editor (Visual Studio Code) & interacting with the XRP ledger](https://coil.com/p/wietse/Coding-the-XRP-ledger-A-beginners-course-/kKu7G0Hhq) by writing your first few lines of Javascript code about a year ago.

### Prerequisite

Prior to following this XUMM SDK tutorial, we assume you setup your environment and followed the steps outlined in my [previous tutorial](https://coil.com/p/wietse/Coding-the-XRP-ledger-A-beginners-course-/kKu7G0Hhq) - meaning your coding environment is ready.

[![Watch this step on Youtube](https://dev-to-uploads.s3.amazonaws.com/i/ke56jhpeq6gmjerebicr.png)](https://youtu.be/9gVK6fp3UOo)

## 1. Get your XUMM API credentials 🔑

Visit the **[XUMM Developer Console](http://apps.xumm.dev/)** by navigating to https://apps.xumm.dev. If this is the first time using the XUMM Developer Console, you can register an account or (if you have one) sign in with your Github account.

[![Watch this step on Youtube](https://dev-to-uploads.s3.amazonaws.com/i/ke56jhpeq6gmjerebicr.png)](https://youtu.be/tdoLbHiMTGo)

After logging in, [create a new application](https://apps.xumm.dev/create-application) by clicking the `Create new application` button. Enter a title & description for your app and upload a nice icon or picture. If you are just going to have some fun with the API, the app title and description do not really matter.

For this tutorial entering a `Webhook URL` is not required.

#### After creating your new XUMM API application, you'll be taken to a page showing your `App ID` and `App Secret`. 

Example credentials (where to look for):
[New App credentials](https://dev-to-uploads.s3.amazonaws.com/i/0ypo6flou5t29b09996t.png)

### ❗ _**Keep these credentials safe!**_

You can always find your App ID in the XUMM Developer Console, but your **App Secret** will be displayed **only once**. You can always re generate it, but doing so will require updating the App Secret in your applications.

### ❗ _**Never share/publish your app credentials!**_

Your **App Secret** is for backend use only. Never use these credentials in a frontend, client side project. When you think your **App Secret** is compromised, please re generate your secret in the XUMM Developer Console.

Congratulations, you have registered your first (?) XUMM App. Let's get started!

## 2. Prepare your project & start coding 🔨

For this example we will use a Javascript (node) environment. More experienced developers will probably use Typescript or Deno, but to keep this tutorial as simple as possible we'll go with plain Javascript.

[![Watch this step on Youtube](https://dev-to-uploads.s3.amazonaws.com/i/ke56jhpeq6gmjerebicr.png)](https://youtu.be/pgSckFopZ6Q)

1. Open the code editor (Visual Studio Code) and create a new file (File » New file)
2. Save (File » Save) your newly created file. Browse to a location that makes sense, create a new folder (eg. `xumm-sdk-tutorial`) and save your file as **`index.js`**
3. Install the XUMM SDK in your project using the '_node package manager_': open the terminal (Terminal » New terminal) and at the prompt, enter `npm install xumm-sdk`
4. Copy/paste this boilerplate code into your `index.js` file

```javascript
const {XummSdk} = require('xumm-sdk')
const Sdk = new XummSdk('Your-App-Id', 'Your-App-Secret')

const main = async () => {
  console.log(`Hi! This is where we'll be writing some code`)
}

main()
```

So: what does this code actually mean/do?

- First we 'import' the XUMM SDK we installed using `npm` in step 3 (above) in our code. We're using the `xumm-sdk` package, and we're calling it `XummSdk` in our code
- Then we initialize a new instance of the SDK, which we call `Sdk`. We'll use this one later on. As we don't interact with the XUMM SDK just yet, the dummy App ID and App Secret do not matter (yet)
- We define a function called `main`, and we 'tell' node that this function is asynchronous (async), meaning we can run code, wait for a response and then continue running more code
- In our function, we just put the code to output a message to the terminal, using the `console.log` method.
- Lastly, we call our `main` function so the code will actually run

Let's run this code. In the terminal panel, type:
```bash
node index.js
```

This will tell `node` to run all code in your `index.js` file. You should receive the message in your terminal, and be back at the prompt:

![Run your code :)](https://dev-to-uploads.s3.amazonaws.com/i/jz54g0zdsnarj8xfszzk.gif)

If you have your App ID and App Secret in place (see placeholder values in code above) you can check if everything is working by asking the XUMM Platform to return your registered XUMM app name.

Replace your line of code containing `console.log(...)` with a `ping` request to the XUMM platform:

```javascript
const appInfo = await Sdk.ping()
```

This line of code calls the `ping` method of the XUMM SDK. We `await` the results (as it may take a brief moment to connect to the XUMM platform and get a response). When we get a response, we're making the results accessible under the name `appInfo`.

Now let's see if we can `console.log` the application name we retrieved from the XUMM platform. When you start typing, Visual Studio Code will suggest available properties:

![Code autocomplete](https://smaviom.dlvr.cloud/pasted_1.png)

Let's `console.log` the returned name. Our `main` method now looks like this:

```javascript
const main = async () => {
  const appInfo = await Sdk.ping()
  console.log(appInfo.application.name)
}
```

Let's run this:
![Sample code](https://osliq6d.dlvr.cloud/123.gif)

Looks like we can reach the XUMM platform 🎉 😎

## 3. Your first payload 🔔
Now we've established a connection to the XUMM platform using the XUMM SDK, it's time to actually send something we can sign. We'll send a "transaction template" to the XUMM platform, called a `Payload`. The lifecycle of a payload is explained [in more detail](https://xumm.readme.io/docs/payload-workflow) in the XUMM API documentation.

Some things to consider:

1. A payload (sign request) sent to the XUMM platform should be formatted as per [XRP ledger transaction specificaftions](https://xrpl.org/transaction-types.html), but some fields may be omitted as XUMM will fill them
2. Usually, a payload (sign request) sent to the XUMM platform will be signed by end users (eg. for a sign in, subscription, payment, ...). For this tutorial, you'll be the initiator and the end user, signing (or rejecting) your own payload.
3. The **first sign request** from a specific XUMM app will **always** have to be **scanned using a QR** code (desktop) **or redirected (mobile)**. Once an end user trusts your application (by signing your request) your application can obtain a user specific `user token` to deliver future sign requests using push notificaftions.
4. This tutorial is running on your own computer, from the terminal. We'll just have to copy/paste a sign request URL to our browser. In a real life application, a nicer flow for end users can be created with redirects / [live status updates](https://xumm.readme.io/docs/payload-status#websocket) / [push & fallback QR codes](https://xumm.readme.io/docs/pushing-sign-requests), etc. That's for another day.

Our first payload will be a `Payment` transaction type. A really basic `Payment` payload you can send to the XUMM platform would be:

{% gist https://gist.github.com/WietseWind/d6e20688389a02f351e3486f1f381779/04ef5c2a0f916baa5d1b5d15458e3a1d7b549150 file=simple-payment.json %}

If (like in the minimal example above) no amount is entered, the end user will be able to select the currency and amount to send in XUMM after opening the sign request. You can add more details to your payload, eg.:

{% gist https://gist.github.com/WietseWind/d6e20688389a02f351e3486f1f381779/04ef5c2a0f916baa5d1b5d15458e3a1d7b549150 file=payment.json %}

This sample specifies an amount in Drops (one XRP is one million Drops, the featured **ten thousand drops** represent 0.01 XRP) and a Memo ([HEX encoded](https://gist.github.com/WietseWind/5cbcc8d15fa2be3ae7d4c20962af0a8e) string). Let's use this example in our code.

We're going to use the `Sdk.payload.create()` method to do this. We'll provide **two** arguments to the `Sdk.payload.create(..., ...)` method.

The first argument will be our payload (like above) and in the second argument we will provide a flag (boolean, `true`) telling the code to return an error, if one occurs.

Our code now looks like this:

{% gist https://gist.github.com/WietseWind/d6e20688389a02f351e3486f1f381779 file=index.js %}

Let's run this code. In the terminal panel, type the previously issued command to run our code: `node index.js`. This should get you your very first XUMM sign request 🎉

![Your first payload!](https://dev-to-uploads.s3.amazonaws.com/i/oi1etslmure3qdj0114z.gif)

## 4. Verify results ⛑ and push 🚀
So you sent a payload to the XUMM platform sing the XUMM SDK, and got yourself a XUMM sign request.

You opened the `next.always` URL, scanned the QR code with your smartphone running XUMM (or from your smartphone: got redirected to the XUMM app) and signed your request. That's already pretty sweet!

But now it's time to verify the payload outcome. And didn't we promise that, once signed, you'd get a `user token` to send your next payload using a Push notification? 

#### Please note: this is where this tutorial gets (even) more technical. Software developers will be able to follow this. If you've never coded before this will probably require some more spare time ;)

This time, we're not just creating a sign request by sending a payload, we're sending a payload and subscribing to payload events. We just used the `Sdk.payload.create()` method. This time we will use the `Sdk.payload.createAndSubscribe()` method. 

The `createAndSubscribe` method accepts the payload in the first argument (just like the `create` method), but the second argument is a _function_ that gets invoked every time when the XUMM platform asynchronously sends an update about the created payload.

The subscription for payload events will stay alive until something is returned by the _function_ (second argument) passed to the `createAndSubscribe` method. Here's a sample:

{% gist https://gist.github.com/WietseWind/e8b07274192020eb65f75b98568b903f file=index.js %}

When you execute this code, you'll immediately see the payload response containing the URL to open the payload. All the way down we console log the `subscription.created` object, containing the created payload data we previously got directly from the `create()` method. 

The other code only executes right after, as the first payload events are fired once the SDK connected to the XUMM platform.

If the event data contains a `signed` property, depending on if the payload has been signed, causes the callback _function_ to `console.log` a message, and return either the event data (if signed) or a boolean `false` (if not signed). By returning something (anything), the subscription ends.

**See if you can get this example to run, open the created payload URL on your desktop, and while the SDK is waiting for status updates, try scanning the QR code with XUMM. You'll see the `opened` event live in your terminal 😎**

When you got this to work, let's wrap this up by adding some code to wait for the `signed: true` event (signing the transaction was successful) so we can get our long awaited `user token` to deliver our future sign requests with a Push notification.

{% gist https://gist.github.com/WietseWind/4d844eb3a77bdca59cdb14c3e511d894 file=index.js %}

When we run this code, open the payload URL on our smartphone, sign it with XUMM, your terminal will look like this. Note the User Token at the end 🎉

![We got ourself a user token!](https://dev-to-uploads.s3.amazonaws.com/i/ndb9f5rp7s95usfay36s.png)

The user token is a unique token for **your XUMM app** in combination with **the specifix XUMM user**. To use the token to deliver a sign requst per Push notification, let's adjust our first (minimal) sample payload to include the user token:

```json
{
  "TransactionType": "Payment",
  "Destination": "rwietsevLFg8XSmG3bEZzFein1g8RBqWDZ",
  "Amout": "10000"
}
```

This payload only contains the XRP ledger specific transaction template. Because we are now going to send XUMM platform specific information as well, we need to wrap the XRP ledger transaction template in an object called `txjson`. We can then add [XUMM platform specific information](https://xumm.readme.io/reference/post-payload) to the payload as well, like `options`, `custom_meta` and the `user_token`.

Wrapping the XRP ledger transaction template in a `txjson` object, and adding the `user_token` property, our payload now looks like this:

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

If we use the user token we got from our previously signed payload and use that in our code, you'll see your sign request popping up on your phone :D

{% gist https://gist.github.com/WietseWind/7f04966c444909649ca43332671fc6d5 file=index.js %}

Now we'll run it, keeping an eye on our phone:

![Sign request Push notification](https://dev-to-uploads.s3.amazonaws.com/i/mh1zt643l9g007kd12ym.gif)

When using this in applications, it would make sense to check the `pushed` property of a created payload. If `false` the Push notification could not be sent (eg. permissions revoked by the user), and your applicaftion should fall back to the QR method. If `pushed` is `true`, it makes sense to show the end user a button to show the QR code anyway if they didn't receive the push notificaftion (eg. changed devices / other device).

## 5. Security & finishing touch 🎉

If you made it this far, you may want to actually build something (even if it's just a hobby project) using the XUMM SDK. If you do, please  **VERIFY THE PAYLOAD RESULT ON LEDGER**.

You can use the **XRPL-TxData** package, or use the `Sdk.getTransaction(txHash)` method to do this by relying on the XUMM platform to fetch the on ledger transaction outcome for you, or eg. by using the `xrpl-txdata` package to verify 'locally'.

By using the `xrpl-txdata` package, you can connect to one of the public XRP ledger nodes and verify the transaction. To do so, add the `xrpl-txdata` package to your project by entering this in the terminal: `npm install xrpl-txdata`. You can verify a transaction as per the [package documentation](https://www.npmjs.com/package/xrpl-txdata).

Pay special attention to the `balanceChanges` response.
**❗ There are several reasons why eg. a payment can yield a different result (eg. lower amount sent) than requested!**

It's your responsibility to check the transaction outcome straight from the XRP ledger, instead of simply relying on XUMM telling you a transaction has been signed. Eg. if:

- The user signed successfully in XUMM, but with a key that is no longer valid for a certain account (because multisign has been configured, an account has been rekeyed, etc.)
- The user sent a Partial Payment (eg. sending EUR to deliver XRP, while the owned amount of EUR was insufficient due to exchange rate slippage)
- The user tried to trick you into accepting a testnet payment, by signing with a funded Testnet account

Please take a look at [this sample code implementing the `xrpl-txdata` package](https://gist.github.com/WietseWind/6c6a3aefc551927ca42e73745e56a062) to verify on ledger balance changes for a signed XUMM payload.

![Verify on ledger transaction](https://lmjk5ut.dlvr.cloud/tx.gif)

## Resources 📚
If you want to dive into the XUMM SDK, patform, documentation, etc. some more, make sure to check out:

- The XUMM SDK [readme](https://www.npmjs.com/package/xumm-sdk)
- The XUMM SDK [source code](https://github.com/XRPL-Labs/XUMM-SDK)
- The XUMM API [documentation](https://xumm.readme.io/docs) & API [reference](https://xumm.readme.io/reference/about)
- XUMM (end user) [support docs](https://support.xumm.app/)
- In case of questions: [support@xumm.app](mailto:support@xumm.app?subject=XUMM+SDK+-+dev.to)

#### Special thanks

Thank you [@technotip](https://twitter.com/technotip) for your tutorial video's!
