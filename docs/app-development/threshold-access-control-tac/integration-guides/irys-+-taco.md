# Irys + TACo

This three-step guide explains how to integrate TACo with [Irys](https://docs.irys.xyz/), thereby enabling end-users to flexibly share and access encrypted data uploaded to permanent storage on [Arweave](https://www.arweave.org/build). \
\
Irys's sub-millisecond uploads/data egress can be parallelized with TACo's low-latency decryption material retrieval, ensuring rapid access to shared data. Provenance features like tx receipts and cryptographic proof-of-time are fully compatible with TACo, and are equally (or arguably more) important for sensitive information and messages. Broadly, combining Irys & TACo offers long-term sovereignty to end-users – i.e. that their private data will remains accessible to qualifying devices forever.&#x20;

## Use cases&#x20;

* **Governance.** Generate tamper-proof, timestamped records of voting activity, enhancing transparency and reducing trust assumptions.&#x20;
* **Connected Vehicles.** Store sensitive real-time vehicle diagnostics and geolocation data, such that the data stream is instantly available when required (e.g. while driving) but not leaked beyond known and legitimate recipients (e.g. a smart city traffic system).&#x20;
* **Private NFTs.** Move beyond the status quo of symbolic receipts stored on centralized platforms, to a world where one owns the _decryption rights_ to a movie, track, in-game asset, or piece of art – trustlessly and in perpetuity.&#x20;

## Installation & dependencies

```typescript
yarn add @nucypher/taco
yarn add @irys/sdk
```

## 1. Specify access conditions & encrypt the data&#x20;

{% hint style="warning" %}
In this guide, the parameters `ritualId = 0` and `domains.TESTNET` are utilized. These refer to an open DKG public key and hacker-facing testnet respectively. Although fully functional and up-to-date with Mainnet, this development environment is **not decentralized** and unsuitable for production or real-world sensitive data. For more information, see the trust assumptions [section](../trust-assumptions/testnet-trust-assumptions/).&#x20;
{% endhint %}

First, we initialize the `taco-web` library.&#x20;

As the data producer, we first create an access condition. Here we use the simple condition `ownsNFT`.\
Data consumers must prove ownership of a specific ERC-721 NFT in order to gain decryption material pertaining to the encrypted message. More on condition types [here](../conditions/).\
\
We encrypt the message using the `ownsNFT` condition. We specify the aforementioned testnet domain and ritualID, and also utilize a standard web3 provider/signer. The output of this function is known as a `messageKit` – a payload containing both the encrypted data and embedded metadata necessary for a qualifying data consumer to access the message. Finally, we convert the `messageKit`to a hex string format, which will help us upload it to Irys in a single transaction.&#x20;

```typescript
import { initialize, encrypt, conditions, domains, toHexString } from '@nucypher/taco';
import { ethers } from "ethers";

// We have to initialize the TACo library first
await initialize();

const web3Provider = new ethers.providers.Web3Provider(window.ethereum);

const ownsNFT = new conditions.predefined.erc721.ERC721Ownership({
  contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
  parameters: [3591],
  chain: 5,
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
const encryptedMessageHex = toHexString(encryptedMessage.toBytes());
```

## 2. Connect to Irys & store the data&#x20;

First, we connect to an Irys node.&#x20;

{% hint style="info" %}
This guide uses an Irys Devnet. This means that storage is not decentralized and will only live on Arweave for **60 days**.&#x20;
{% endhint %}

We then construct a JSON object from the `encryptedMessageHex`. Then upload the encrypted data to Arweave.

<pre class="language-typescript"><code class="lang-typescript">import { WebIrys } from '@irys/sdk';

const token = 'matic';
const wallet = { rpcUrl: https://rpc-amoy.polygon.technology/, name: 'ethersv5', web3Provider };
const webIrys = new WebIrys({ url: https://node2.irys.xyz, token, wallet });

await webIrys.ready();

<strong>const dataToUpload = JSON.stringify(encryptedMessageHex);
</strong>const receipt = await webIrys.upload(dataToUpload);

console.log(`Data uploaded ==> https://gateway.irys.xyz/${receipt.id}`);
</code></pre>

## 3. Retrieve & decrypt the data

As the data consumer, we first retrieve the encrypted data from Arweave&#x20;

We then decrypt the data.&#x20;

```typescript
import { getPorterUri, ThresholdMessageKit, decrypt } from '@nucypher/taco';

const response = await fetch(`https://gateway.irys.xyz/${receiptId}`);
const dataJson = await response.text();
const encryptedMessage = ThresholdMessageKit.fromBytes(
  Buffer.from(JSON.parse(dataJson), 'hex'),
);

const decryptedMessage = await decrypt(
  web3Provider,
  domains.TESTNET,
  encryptedMessage,
  getPorterUri(domains.TESTNET),
  web3Provider.getSigner(),
);

console.log(decryptedMessage);
```

## Using Irys & TACo in production&#x20;

