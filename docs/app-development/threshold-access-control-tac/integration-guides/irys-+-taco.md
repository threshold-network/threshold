# Irys + TACo

This guide explains how to integrate TACo with [Irys](https://docs.irys.xyz/), thereby enabling end-users to flexibly share and access encrypted data uploaded to permanent storage on [Arweave](https://www.arweave.org/build). \
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

## Encrypt Data

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

const message = "my secret message";

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

This code initializes the TACo library and sets up a [condition](../conditions/) that requires ownership of a specific ERC-721 NFT for decryption. It then encrypts a secret message using this condition, specifying the testnet domain and utilizing the connected web3 provider and signer for the encryption process. The result is a `messageKit` which contains the encrypted data and metadata necessary for controlled decryption.

{% hint style="info" %}
`We` are using `ritualId = 0` and `domains.TESTNET` in this example. These correspond to our [publicly available development environment](get-started-with-tac.md) and are **not** suitable for production.&#x20;
{% endhint %}

## Storing the Data&#x20;

Next, connect to an Irys node and upload the encrypted data to Arweave.

<pre class="language-typescript"><code class="lang-typescript">import { WebIrys } from '@irys/sdk';

const token = 'matic';
const wallet = { rpcUrl: https://rpc-amoy.polygon.technology/, name: 'ethersv5', web3Provider };
const webIrys = new WebIrys({ url: https://node2.irys.xyz, token, wallet });

await webIrys.ready();

<strong>const dataToUpload = JSON.stringify(encryptedMessageHex);
</strong>const receipt = await webIrys.upload(dataToUpload);

console.log(`Data uploaded ==> https://gateway.irys.xyz/${receipt.id}`);
</code></pre>

## Retrieving the Data

Finally, retrieve the encrypted data from Arweave and decrypt it.

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
