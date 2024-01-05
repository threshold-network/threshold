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

<pre class="language-bash"><code class="lang-bash"><strong>$ npm install @nucypher/taco ethers@5.7.2
</strong></code></pre>

## Configuration

In order to run the code examples below, you will need the `ritualId` encryption parameter. Your wallet address (encryptor) will also have to be allow-listed for this specific ritual. Please reach out to us [here](https://discord.com/channels/866378471868727316/870383642751430666) in order to receive  `ritualId` and allow-list access, or use the [publicly available testnet rituals](integration-guide/get-started-with-tac.md#testnet-configuration).

## Encrypt your data

With `ritualId` and [a web3 provider from `ethers`](https://docs.ethers.org/v5/api/providers/#providers-getDefaultProvider), we can `taco.encrypt` our data:

<pre class="language-typescript"><code class="lang-typescript">import { initialize, encrypt, conditions, domains } from '@nucypher/taco';
import { ethers } from "ethers";

// We have to initialize the TACo library first
await initialize();

const web3Provider = new ethers.providers.Web3Provider(window.ethereum);

const ownsNFT = new conditions.predefined.ERC721Ownership({
  contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
  parameters: [3591],
  chain: 5,
});

const message = "my secret message";

const messageKit = await encrypt(
<strong>  web3Provider,
</strong><strong>  domains.TESTNET,
</strong>  message,
  ownsNFT,
  ritualId,
  web3Provider.getSigner() 
);
</code></pre>

## Decrypt your data

Now we just have to pass the  `messageKit` to the intended recipient:

<pre class="language-typescript"><code class="lang-typescript"><strong>import { initialize, decrypt, domains, getPorterUri } from '@nucypher/taco';
</strong>import { ethers } from "ethers";

// We have to initialize the TACo library first
await initialize();

const web3Provider = new ethers.providers.Web3Provider(window.ethereum); 

const decryptedMessage = await decrypt(
  web3Provider,
  domains.TESTNET,
  messageKit,
  getPorterUri(domains.TESTNET),
  web3Provider.getSigner()
);
</code></pre>

Since `ownsNFT` condition refers to an NFT owned by the recipient, `decrypt` call will prompt the recipient to sign a message and prove the ownership of the caller's wallet.

## Next steps

Learn more about using TACo on the testnet - [get-started-with-tac.md](integration-guide/get-started-with-tac.md "mention").&#x20;

Learn more about testnet trust assumptions and limitations - [cbd-proof-of-concept-version](trust-assumptions/cbd-proof-of-concept-version/ "mention")

### Example applications

The following samples showcase integrations with React-based web apps, and serve as an 'end-to-end' reference for creating conditions-based encryption & decryption:

* [`nucypher-ts/demos`](https://github.com/nucypher/nucypher-ts/tree/main/demos)
* [`nucypher-ts/examples/taco`](https://github.com/nucypher/nucypher-ts/tree/main/examples/taco)
* [`nucypher/alpha-leaks-demo`](https://github.com/nucypher/alpha-leaks-demo)
