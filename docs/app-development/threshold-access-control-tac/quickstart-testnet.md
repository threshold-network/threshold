---
description: TACo SDK allows you to use threshold encryption & decryption in your apps.
---

# Quickstart (Testnet)

In just a few minutes you will able to:

* Encrypt your data
* Describe decryption conditions with onchain data
* Threshold-decrypt your data when these conditions are met

## Installation

Install `taco` and `ethers` with your favorite package manager:

<pre class="language-bash"><code class="lang-bash"><strong>$ npm install @threshold-network/taco ethers@5.7.2
</strong></code></pre>

## Configuration

In order to run the code examples below, you will need the `ritualId` encryption parameter. Please reach out to us [here](https://discord.com/channels/866378471868727316/870383642751430666) if you don't already have it.

## Encrypt your data

With `ritualId` and [a web3 provider from `ethers`](https://docs.ethers.org/v5/api/providers/#providers-getDefaultProvider), we can `taco.encrypt` our data:

<pre class="language-typescript"><code class="lang-typescript">import taco, { conditions } from '@threshold-network/taco'
import { ethers } from "ethers";

const web3Provider = ethers.getDefaultProvider();

const ownsNFT = new conditions.predefined.ERC721Ownership({
  contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
  parameters: [3591],
  chain: 5,
});

const messageKit = await taco.encrypt(
<strong>  web3Provider,
</strong>  message,
  ownsNFT,
  ritualId 
);

</code></pre>

## Decrypt your data

Now we just have to pass the  `messageKit` to the intended recipient:

```typescript
import taco from '@threshold-network/taco'
import { ethers } from "ethers";

const web3Provider = ethers.getDefaultProvider(); 

const decryptedMessage = await taco.decrypt(
  web3Provider,
  messageKit,
  web3Provider.getSigner()
);

```

Since `ownsNFT` condition refers to an NFT owned by the recipient, `taco.decrypt` call will prompt the recipient to sign a message and prove the ownership of the caller's wallet.

## Next steps

TODO: Link to TACo explainer? High-level overview + trust assumptions

TODO: A list of the most popular/interesting/handy "recipes" for using/integrating TACo into your app.&#x20;

* Learn more about conditions here
* ... about composing conditions here
* ...
