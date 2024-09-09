# Irys + TACo

This three-step guide explains how to integrate TACo with [Irys](https://docs.irys.xyz/), thereby enabling end-users to flexibly share/access encrypted data uploaded to permanent storage on [Arweave](https://www.arweave.org/build). \
\
There are plenty of reasons to combine these technologies. Irys's sub-millisecond upload & data egress can be parallelized with TACo's low-latency decryption flow, ensuring rapid access to shared data. Provenance features like transaction receipts and cryptographic proof-of-time are fully compatible with TACo and are equally (or arguably more) important for sensitive information and messages. Broadly, integrating Irys & TACo offers long-term sovereignty to end-users – i.e. that their private data will remain accessible to qualifying devices forever.&#x20;

## Use cases&#x20;

* **Governance.** Generate tamper-proof, timestamped records of voting activity, enhancing transparency and reducing trust assumptions.&#x20;
* **Connected Vehicles.** Store sensitive real-time vehicle diagnostics and geolocation data, such that the data stream is instantly available when required (e.g. while driving) but not leaked beyond known and legitimate recipients (e.g. a smart city traffic system).&#x20;
* **Private NFTs.** Move beyond the status quo of symbolic receipts stored on centralized platforms, to a world where one owns the _decryption rights_ to a movie, track, in-game asset, or piece of art – trustlessly and in perpetuity.&#x20;

## Installation & dependencies

```typescript
yarn add @nucypher/taco
yarn add @nucypher/taco-auth
yarn add @irys/sdk
```

## 1. Define access condition & encrypt the data&#x20;

{% hint style="warning" %}
This guide utilizes the parameters `ritualId = 0` and `domains.TESTNET`. These refer to an open DKG public key and hacker-facing stable testnet respectively. Although fully functional and up-to-date with Mainnet, this development environment is **not decentralized** and unsuitable for real-world sensitive data. For more information, see the trust assumptions [section](../trust-assumptions/testnet-trust-assumptions/).&#x20;
{% endhint %}

First, we initialize the `taco-web` [library](https://github.com/nucypher/taco-web).&#x20;

As the data producer, we create an access condition. Here we use the simple condition `ownsNFT` – data consumers must prove ownership of a specific ERC-721 NFT in order to gain decryption material pertaining to the encrypted message. More on condition types [here](../conditions/).\
\
We encrypt the message using the `ownsNFT` condition, specifying the aforementioned testnet `domain` and `ritualID`, and a standard web3 provider/signer. The output of this function is a `messageKit` – a payload containing both the encrypted data and embedded condition metadata necessary for a qualifying data consumer to decrypt the message. Finally, we convert the `messageKit`to a hex string format, which will help us upload it via Irys in a single transaction.&#x20;

<pre class="language-typescript"><code class="lang-typescript">import { encrypt, conditions, domains, initialize, toHexString } from '@nucypher/taco';
<strong>import { ethers } from "ethers";
</strong>
// We have to initialize the TACo library first
await initialize();

const web3Provider = new ethers.providers.Web3Provider(window.ethereum);

const ownsNFT = new conditions.predefined.erc721.ERC721Ownership({
  contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
  parameters: [3591],
  chain: 11155111,
});
const ritualId = 0

const message = "this will be here forever";

const messageKit = await encrypt(
  web3Provider,
  domains.TESTNET,
  message,
  ownsNFT,
  ritualId,
  web3Provider.getSigner() 
);
const encryptedMessageHex = toHexString(messageKit.toBytes());
</code></pre>

## 2. Connect to Irys & store the data&#x20;

First, we connect to an Irys [Devnet](https://docs.irys.xyz/developer-docs/using-devnet) node. This requires funding a wallet with any of the devnet [tokens](https://docs.irys.xyz/overview/supported-tokens) supported by Irys. In this example, we're using Polygon (Amoy) MATIC.&#x20;

{% hint style="info" %}
The Irys Devnet is for testing purposes only. It is not decentralized and data will only remain on Irys servers for \~**60 days**. See the final section on using TACo & Irys in production.&#x20;
{% endhint %}

We then construct a single JSON object from the `encryptedMessageHex`. We can now upload the encrypted data to Arweave, which will be retrievable once an Irys [gateway](https://docs.irys.xyz/developer-docs/downloading) has indexed the data. This is identifiable via the `receiptID`, which is provided to the data consumer via a side-channel.&#x20;

<pre class="language-typescript"><code class="lang-typescript">import { WebIrys } from '@irys/sdk';

const token = 'matic';
const network = 'devnet';
const wallet = { rpcUrl: https://rpc-amoy.polygon.technology/, name: 'ethersv5', web3Provider };
const webIrys = new WebIrys({ network, token, wallet });

await webIrys.ready();

<strong>const dataToUpload = JSON.stringify(encryptedMessageHex);
</strong>const receipt = await webIrys.upload(dataToUpload);

console.log(`Data uploaded ==> https://gateway.irys.xyz/${receipt.id}`);
</code></pre>

## 3. Retrieve & decrypt the data

From the data consumer's perspective, we now use the `receiptID` to find and retrieve the encrypted payload via an Irys gateway. Note that the same data identifier works with Arweave gateways.&#x20;

Finally, we prove we own a wallet that this wallet holds the correct NFT, retrieve fragments of decryption material from TACo nodes, assemble these fragments locally, and decrypt the payload. All of these steps are contained in the `decrypt()` function below.&#x20;

```typescript
import { conditions, decrypt, ThresholdMessageKit } from '@nucypher/taco';
import { EIP4361AuthProvider, USER_ADDRESS_PARAM_DEFAULT } from '@nucypher/taco-auth';

const response = await fetch(`https://gateway.irys.xyz/${receipt.id}`);
const dataJson = await response.text();
const encryptedMessage = ThresholdMessageKit.fromBytes(
  Buffer.from(JSON.parse(dataJson), 'hex'),
);

// auth provider when condition contains ":userAddress" context variable
// the decryptor user must provide a signature to prove ownership of the wallet address
const authProvider = new EIP4361AuthProvider(
  web3Provider,
  web3Provider.getSigner(),
);
const conditionContext =
  conditions.context.ConditionContext.fromMessageKit(encryptedMessage);
conditionContext.addAuthProvider(USER_ADDRESS_PARAM_DEFAULT, authProvider);

const decryptedMessage = await decrypt(
  web3Provider,
  domains.TESTNET,
  encryptedMessage,
  conditionContext,
);

console.log(decryptedMessage);
```

## Example integration

Check out this [token-gated photo album](https://github.com/lukecd/irys-threshold), an intuitive mini-app that demonstrates the power and simplicity of using Irys & TACo in concert. Images are encrypted via the TACo API and stored on-chain via Irys. To view the images, users must prove they hold special-purpose NFT.&#x20;

## Using Irys & TACo in production&#x20;

As noted, the parameters specified in this guide are for testing and hacking only. For real-world use cases where uploaded data should remain private & permanent, production versions of Irys & TACo are required:

* For Irys, connect to a Mainnet Node rather than a Devnet node. This requires a wallet [funded](https://docs.irys.xyz/overview/cost-to-upload) with any of the supported Mainnet payment tokens.&#x20;
* For TACo, a funded Mainnet `ritualID` is required – this connects the encrypt/decrypt API to a cohort of independently operated nodes and corresponds to a DKG public key generated by independent parties. A dedicated `ritualID` for Irys + TACo projects will be sponsored soon. Watch for updates here or in the Discord [#taco](https://discord.com/channels/866378471868727316/870383642751430666) channel.&#x20;
