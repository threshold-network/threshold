---
description: >-
  The TACo SDK allows you to use threshold encryption & decryption in your
  application.
---

# Quickstart (Testnet)

In just a few minutes you will able to:

* **Define decryption conditions** – these are predefined rules or criteria that must be fulfilled before the encrypted data can be decrypted.
* **Encrypt data & assign decryption conditions** – when you encrypt data, you not only secure it but also tie the decryption process to the conditions you defined.
* **Threshold-decrypt data** – once the decryption conditions are met and validated by a threshold of TACo nodes, decryption can occur.

## Installation

Install `taco` , `taco-auth`, and `ethers` with your favorite package manager:

<pre class="language-bash"><code class="lang-bash"><strong>$ npm install @nucypher/taco @nucypher/taco-auth ethers@5.7.2
</strong></code></pre>

## Configuration

To run the code examples below, you will need the `ritualId` encryption parameter. **In production**, your wallet address (encryptor) will also have to be allow-listed for this specific ritual. Please reach out to us [here](https://discord.com/channels/866378471868727316/870383642751430666) to receive a  `ritualId` and allow-list access. \
\
Additionally, we have [publicly available testnet rituals](integration-guides/get-started-with-tac.md#testnet-configuration) for use when developing your apps.

## Define decryption condition and encrypt data&#x20;

With `ritualId` and [a web3 provider from `ethers`](https://docs.ethers.org/v5/api/providers/#providers-getDefaultProvider), we can `taco.encrypt` our data:

<pre class="language-typescript"><code class="lang-typescript">import { initialize, encrypt, domains } from '@nucypher/taco';
import { ethers } from "ethers";

// We have to initialize the TACo library first
await initialize();

// Define decryption condition
const ownsNFT = new conditions.predefined.erc721.ERC721Ownership({
  contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
  parameters: [3591],
  chain: 11155111,  // sepolia
});

const web3Provider = new ethers.providers.Web3Provider(window.ethereum);

const message = "my secret message";
const ritualId = 0

// encrypt data
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

<pre class="language-typescript"><code class="lang-typescript"><strong>import { conditions, decrypt, domains, initialize,  } from '@nucypher/taco';
</strong>import { EIP4361AuthProvider, USER_ADDRESS_PARAM_DEFAULT } from '@nucypher/taco-auth';
import { ethers } from "ethers";

// We have to initialize the TACo library first
await initialize();

const web3Provider = new ethers.providers.Web3Provider(window.ethereum); 

const conditionContext =
  conditions.context.ConditionContext.fromMessageKit(messageKit);
  
// auth provider when condition contains ":userAddress" context variable
// the decryptor user must provide a signature to prove ownership of their wallet address
const authProvider = new EIP4361AuthProvider(
  web3Provider,
  web3Provider.getSigner(),
);
conditionContext.addAuthProvider(USER_ADDRESS_PARAM_DEFAULT, authProvider);

const decryptedMessage = await decrypt(
  web3Provider,
  domains.TESTNET,
  messageKit,
  conditionContext,
);
</code></pre>

Since `ownsNFT` condition refers to an NFT owned by the recipient, `decrypt` call will prompt the recipient to sign a message and prove the ownership of the caller's wallet.

## Next steps

Learn more about using TACo on the testnet - [get-started-with-tac.md](integration-guides/get-started-with-tac.md "mention").&#x20;

Learn more about testnet trust assumptions and limitations - [testnet-trust-assumptions](trust-assumptions/testnet-trust-assumptions/ "mention")

### Example applications

The following samples showcase integrations with React-based web apps, and serve as an 'end-to-end' reference for creating conditions-based encryption & decryption:

* [`taco-web/demos`](https://github.com/nucypher/taco-web/tree/main/demos)
* [`taco-web/examples/taco`](https://github.com/nucypher/taco-web/tree/main/examples/taco)
