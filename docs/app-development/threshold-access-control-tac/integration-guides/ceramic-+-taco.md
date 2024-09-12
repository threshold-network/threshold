# Ceramic + TACo

This guide demonstrates how to integrate TACo with one of Ceramic's data services, [ComposeDB](https://developers.ceramic.network/docs/composedb/getting-started). TACo and Ceramic's data services are essential and mutually complementary components of the Web3 stack, offering developers ‘Web 2.0’ functionality without compromising on decentralization.

On completion of this tutorial, it will be possible to:&#x20;

1. Specify fine-grained access control logic for encrypted data/streams saved to the ComposeDB graph database.
2. Have data requesters authenticate themselves with a reused Ceramic sign-in (a Sign-In With Ethereum message) in order to decrypt the retrieved data/stream.
3. Securely gate-keep any format, size or throughput of data stream, with access collectively managed by a permissionless and customizable group of TACo nodes.&#x20;

## ComposeDB Overview

A data service built on Ceramic, the ComposeDB graph database offers enhanced composability and ease of querying. ComposeDB comes with native GraphQL support and automatically splits read/write load for additional performance. When running a Ceramic node with ComposeDB, developers can define their[ data models](https://developers.ceramic.network/docs/composedb/create-your-composite) using GraphQL, or choose to begin indexing on existing data models already defined by the community, or both.

ComposeDB can be leveraged in concert with TACo, to ensure that returned data and messages are only decryptable by parties satisfying pre-specified conditions. Interactions with ComposeDB (via the Ceramic network) and TACo (via the Threshold network) can be architected to occur in parallel, which means non-cumulative latency. TACo's Single Sign On framework also provides the basis for extensions to broader user identity and authentication, including compatibility with Ceramic's library of [DIDs](https://github.com/ceramicnetwork/js-did) (e.g. PKH-Ethereum).

## Use Cases

* **Social networks & Knowledge Bases**. Leverage Ceramic's verifiable credentials and TACo's credential-based decryption to ensure that private user-generated content is only viewable by those who are supposed to see it, and nobody else.
* **IoT event streams**. Let sensitive data flow from sensors to legitimate recipients, without trusting an intermediary server to handle the routing and harvest metadata. For example, a medical professional can be issued a temporary access token if a patient's wearable output data rises above a certain threshold.
* **LLM chatbots**. Messages to and from a chatbot should be 100% private, not mined by a UX-providing intermediary. Harness Ceramic's web-scale transaction processing and TACo's per-message encryption granularity to provide a smooth and private experience for users of LLM interfaces.

## Demo Application

In this section of the tutorial, we will set up a simple browser-based messaging application that makes combining ComposeDB and TACo more intuitive. This involves running a local Ceramic node, to which TACo-encrypted messages are saved and immediately queryable by data requestors.

{% hint style="info" %}
This demo [repo](https://github.com/nucypher/taco-composedb) is based on a fork of[ ](https://github.com/ceramicstudio/lit-composedb?tab=readme-ov-file)ceramicstudio’s lit-composedb [repo](https://github.com/ceramicstudio/lit-composedb/), with the TACo library replacing LIT. This demonstrates how applications that have already integrated LIT’s permissioned service can easily substitute it for TACo’s decentralized access control plugin.
{% endhint %}

This demo requires:

* A [Metamask](https://metamask.io/) wallet with [Polygon Amoy](https://polygon.technology/blog/introducing-the-amoy-testnet-for-polygon-pos) testnet added, and multiple accounts to mimic a real-world decryption flow.&#x20;
* A positive balance of Polygon Amoy testnet tokens (> 0.00 MATIC) held in one of the accounts, in order to satisfy the default access conditions.&#x20;

First, we’ll clone the reference repository.

```
git clone https://github.com/nucypher/taco-composedb
```

Next, we install the dependencies. This requires _node v16_ running in our terminal.

```
npm install 
```

&#x20;Next, we’ll generate parameters for the local Ceramic node. This command defines the ComposeDB config file, and generates the admin seed & admin DID credentials.

```
npm run generate
```

The commands above only need to be executed once.&#x20;

Finally, we launch the browser application and Ceramic node. This is the only step required for subsequent runs.

```
npm run dev
```

Open the app on [http://localhost:3000](http://localhost:3000) and sign in with Ceramic. Note that this signature can be reused to authenticate the data consumer later in the flow, if we maintain the same session.\
\
However, we begin as the _data producer_, and enter a message into the chat box element: &#x20;

<div align="left">

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXcCOLHqCBtpWUg02CuE1ps7SbDNU4LFH4GCu3gfG9smYBejjgVqSOETy6ocBjUBppbVYy2qPllmuqKHDk1R1D7Qcf1o_85WiJczBaA6CUDPB5nt4Ctpi6GFI1SLgAtJiqSrONtDIMMOpsnwbF5OzrbNgP6Y?key=kKsOISObNWoPp0VzTRWAiQ" alt="" width="563"><figcaption></figcaption></figure>

</div>

When we click Send, the app will prompt us to sign the message. The TACo API requires a specific signature (i.e. not SIWE) to encrypt data, so that later, TACo nodes are able to validate that (1) we are authorized to encrypt, using that particular group of nodes, and (2) we specified the conditions for decryption.\
\
Once we have provided this signature, the message is encrypted locally and we're presented with a ciphertext:&#x20;

<div align="left">

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXe8gwQzPkMtVw1Gefjn4If41uNdZuzsa3aRoyrgAHyGE_40YsfkNC2Y-cHs91dpaxsaVPJl5DLZyc7f8hIsjYhj3RmdeNdZXm3i4gJFMI0RGmgMQuDkhKGXuuQQbIEewVY0c_y_74lIWh63Yjy_GYhkxgtG?key=kKsOISObNWoPp0VzTRWAiQ" alt="" width="563"><figcaption></figcaption></figure>

</div>

We now switch to the _data consumer_’s perspective by disconnecting and reconnecting with a new account in Metamask. This mimics the majority of use cases in which a data consumer will make this request from a new device or identity.&#x20;

Having established a session with a new account, we will need to authenticate ourselves. When we hit the Decrypt button, it prompts us to sign in with Ceramic. Note that this signature is set to expire after 2 hours, during which time we can decrypt as many messages as we like, as the signature will remain cached.

Our authenticated identity – a unique EVM wallet address that we have proven we control – must now satisfy the prespecified conditions. In this example, the qualifying conditions are the wallet holding any amount of MATIC in a Polygon Amoy wallet greater than zero. &#x20;

{% hint style="info" %}
Note that in this demo, the end-user is not choosing the conditions for data access via the browser UX. However, developers can modify these default access conditions, or enable the user to specify access conditions directly. This functionality is explored in later sections.
{% endhint %}

We’ll first attempt to decrypt whilst failing to fulfill the conditions. In this instance, this account contains zero MATIC:&#x20;

<div align="left">

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXfhwhzb9Ffw9K0j6QL__QVP8J14nc8DUJXcxLwqwQK2gnyi5FO0FZwueBP9KHF-s2BZICz2XiPNACxTMt93mWfGfu9gYZHPc2plQyScvevmgjjEi5ov5g5Nd_ttC9OJT3MK_8iXmpkKDwpmryPpnw0FTps?key=kKsOISObNWoPp0VzTRWAiQ" alt="" width="375"><figcaption></figcaption></figure>

</div>

As expected, we are denied access to the plaintext:&#x20;

<div align="left">

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXeHuX6s6050xVxorFAHDWfygZJyIyaneOxu5Zy1i_O4vGts3N2fSnC39Im5p1h8jPCQYY-1e_ENU_FhZtyWH8ITDOeEXpby7qRZTzfLTB_VqfQMKwSfJokgH3MVmFnrqRAxxc8zxQGZIe-wtZGIVLhXpKM?key=kKsOISObNWoPp0VzTRWAiQ" alt="" width="563"><figcaption></figcaption></figure>

</div>

We’ll now fund the wallet with MATIC in order to satisfy the requisite conditions. We could also switch accounts to a wallet with the requisite balance.

<div align="left">

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXftAdpAkl2ZTX9SWyAs5yk10HV8iSTIgxHjnj5VtGOxiLlfTWqYoe54NgI-lcA0YJj7wM17TvmFcNoFSHW3zSLPnMk48IvGaFoTz2iG5XJxoYqynA6DqgjjvjbFczaYmaCToSklW2RVoT80wHZpjqIHi3Wo?key=kKsOISObNWoPp0VzTRWAiQ" alt="" width="375"><figcaption></figcaption></figure>

</div>

This time, when we hit the Decrypt button and ping the TACo API, the assigned group of nodes validates our fulfillment of the access conditions – without a need to authenticate again. The wallet now satisfies the required access condition and the nodes will subsequently deliver the decryption fragments required for us to decrypt. We assemble the fragments locally, and can view the original plaintext message:

<div align="left">

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXfjKA2T8gV6AsDVlmz6l_eiCnHhfTIgSuW2AfNiX-49NJjg937FbZTYBwCl54pqI6zDb-aGURqUIXvUmUc4MNjH03LElIKxGksOOts93YtBLNWyKEociujF8nG1s5rBP0PX98dZffqKVa99r2EqMamqVJe0?key=kKsOISObNWoPp0VzTRWAiQ" alt="" width="563"><figcaption></figcaption></figure>

</div>

To clear the session data and restart the demo, click the _Reset_ button in the navigation bar.

{% hint style="warning" %}
Note that this tutorial utilizes the parameters `ritualId` = 0 and `domains.TESTNET`. These refer to an permissionless DKG public key and hacker-facing testnet respectively. Although fully functional and up-to-date with Mainnet, this [development environment](https://docs.threshold.network/app-development/threshold-access-control-tac/integration-guides/get-started-with-tac#testnet-configuration) is not decentralized and unsuitable for real-world sensitive data. For more information, see the trust assumptions [section](https://docs.threshold.network/app-development/threshold-access-control-tac/trust-assumptions).
{% endhint %}

Having illustrated the basic concepts via the example application, we’ll now look at the underlying code and how to configure it to your use case.

## Specifying conditions & authentication

There are two distinct ways in which a _data consumer_ must prove their right to access the private data. The _data producer_ can customize both of these, and combine them in any way they see fit.&#x20;

1. _Data consumers_ must **authenticate** themselves – i.e. prove their identity. In this tutorial, we chose an EVM-based identity. More concretely, we require TACo nodes to process the claim that the requestor has already authenticated themselves within the app they are using, and they did so via Sign In With Ethereum (SIWE).
2. _Data consumers_ must satisfy a set of **conditions** – i.e. some public web state that TACo nodes can either validate or invalidate. In the demo above, we simply required requestors to hold testnet MATIC. Here, we’re going to build on this by adding a time-based condition, and then combine them into a ConditionSet.&#x20;

We’ll begin by specifying the authentication method and the first condition. This is the same configuration as the demo above and can be viewed in the repository [here](https://github.com/nucypher/taco-composedb/blob/main/src/fragments/chatinputbox.tsx#L26-L34). The method\
is an RPC function that checks the balance of the data consumer based on the identity they provide, which in this case will be authenticated via a EIP4361 (SIWE) message already utilized by the application. The chain ID refers to Polygon Amoy.

```typescript
import { conditions } from "@nucypher/taco"; 

const rpcCondition = new conditions.base.rpc.RpcCondition({
    chain: 80002,
    method: 'eth_getBalance',
    parameters: [':userAddressExternalEIP4361'],
    returnValueTest: {
        comparator: '>',
        value: 0,
    },
});
```

Next, we’ll logically combine this condition with a second  condition via a `CompoundCondition`using the `AND` operator, which means both conditions must be satisfied for data access. The second condition allows access only until the end of 2024, via a standard block timestamp.&#x20;

<pre class="language-typescript"><code class="lang-typescript">const timeBox = new conditions.base.time.TimeCondition({
    chain: 11155111,
    returnValueTest: {
        comparator: '&#x3C;=',
        value: 1735689599,
    },
});
<strong>
</strong><strong>const twoConditions = new conditions.compound.CompoundCondition({
</strong>    operator: 'and',
    operands: [rpcCondition, timeBox],
});
</code></pre>

Read more about condition types [here](https://docs.threshold.network/app-development/threshold-access-control-tac/conditions).

## Encrypting & saving the data

Then, we put it all together. We specify the aforementioned testnet `domain` and `ritualId`, and also utilize a standard web3 provider/signer. The output of this function is known as a `messageKit` – a payload containing both the encrypted data and embedded metadata necessary for a qualifying _data consumer_ to access the message.&#x20;

```typescript
import { initialize, encrypt, conditions, domains, toHexString } from '@nucypher/taco';
import { ethers } from "ethers";

// We have to initialize the TACo library first
await initialize();

const web3Provider = new ethers.providers.Web3Provider(window.ethereum);
const ritualId = 0
const message = "I cannot trust a centralized access control layer with this message.";

const messageKit = await encrypt(
    web3Provider,
    domains.TESTNET,
    message,
    twoConditions,
    ritualId,
    web3Provider.getSigner()
);
const encryptedMessageHex = toHexString(messageKit.toBytes());
```

## Querying & decrypting the data&#x20;

We're now going to enable _data consumers_ to access the underlying data, if and only if the two conditions we specified are satisfied. Data consumers interact with the TACo API via the `decrypt` function, including the following arguments:&#x20;

* `Provider` – Web3 provider to connect to Polygon.
* `Domain` – which TACo network (`Mainnet`, `Testnet` ).
* `encryptedMessage`/`ThresholdMessageKit` – this contains the encrypted plaintext and the access conditions, supplied to the _data consumer_ via a side channel.
* `conditionContext` – this enables on-the-fly, programmatic population of [context variable](../conditions/conditioncontext-and-context-variables.md) values used within conditions, the most important being data consumer authentication. Developers can predicate certain authentication methods on certain conditions. For example:
  * If conditions are based around EVM state, authenticate via SIWE.
  * (In future versions) If conditions are based around social account ownership, authenticate via OAuth.&#x20;

```typescript
import {conditions, decrypt, Domain, encrypt, ThresholdMessageKit} from '@nucypher/taco';
import {ethers} from "ethers";

export async function decryptWithTACo(
    encryptedMessage: ThresholdMessageKit,
    domain: Domain,
    conditionContext?: conditions.context.ConditionContext
): Promise<Uint8Array> {
    const provider = new ethers.providers.Web3Provider(window.ethereum);
    return await decrypt(
        provider,
        domain,
        encryptedMessage,
        conditionContext,
    )
}
```

In this case, the data consumer must also provide an existing SIWE message signature, to prove their on-chain identity. This is shown below, along with the definition of the function `decryptWithTaco`:

```tsx
const mkB64 = message.ciphertext;
const mkBytes = await decodeB64(mkB64);
const thresholdMessageKit = ThresholdMessageKit.fromBytes(mkBytes);

// obtain existing SIWE message and signature from application
const {messageStr, signature} = await getCeramicSiweInfo(currentAddress);

// create corresponding user authentication provider
const singleSignOnEIP4361AuthProvider = await SingleSignOnEIP4361AuthProvider.fromExistingSiweInfo(messageStr, signature);
const conditionContext = conditions.context.ConditionContext.fromMessageKit(thresholdMessageKit);
conditionContext.addAuthProvider(USER_ADDRESS_PARAM_EXTERNAL_EIP4361, singleSignOnEIP4361AuthProvider);

// decrypt the data
decryptedMessageBytes = await decryptWithTACo(
    thresholdMessageKit,
    domains.TESTNET,
    conditionContext,
);
```

## Using ComposeDB & TACo in production&#x20;

* For Ceramic, connect to Mainnet (`domains.MAINNET`).
* For TACo, a funded Mainnet `ritualID` is required – this connects the encrypt/decrypt API to a cohort of independently operated nodes, and corresponds to a DKG public key generated by independent parties. A dedicated `ritualID` for Ceramic + TACo projects will be sponsored soon. Watch for updates here or in the Discord[ #taco](https://discord.com/channels/866378471868727316/870383642751430666) channel.

As noted, the parameters specified in this guide are for testing and hacking only. For real-world use cases where uploaded data should remain private & permanent, the production version of TACo is required.
