# Integration Guide

This guide provides an overview of `@nucypher/taco`. In this guide, we will walk through a more complete usage example and explain some of the `taco` concepts.&#x20;

Note that the underlying [trust assumptions](../trust-assumptions/) vary between versions and technologies; these are explained in detail on these pages:

* [cbd-mainnet-version.md](../trust-assumptions/cbd-mainnet-version.md "mention")
* [cbd-proof-of-concept-version](../trust-assumptions/cbd-proof-of-concept-version/ "mention")

## 0. Pick an appropriate `taco` version

Before we install `taco`, we need to consider which network we would like to use:

* `mainnet` - a production MAINNET environment
* `tapir` - a stable `TESTNET`, for testing integrations, and matches `mainnet`
* `lynx`- a bleeding-edge `DEVNET`, used for internal development and future features

{% hint style="info" %}
`tapir` is the stable testnet recommended for developers.
{% endhint %}

Once you picked a network, head over to npmjs.com to find the current version [for a given network](https://www.npmjs.com/package/@nucypher/taco?activeTab=versions). To find the appriopriate network, refer to the "Tags" column in the "Current Tags" section:

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption><p>"Current Tags" section of @nucypher/taco package, with their respective supported networks under the "Tag" column</p></figcaption></figure>

You can learn more about the current state of `mainnet` and test networks here:

* [mainnet-beta-program.md](mainnet-beta-program.md "mention")
* [get-started-with-tac.md](get-started-with-tac.md "mention")

## 1. Install and integrate `taco`

To begin, we need to install the `taco` library:

```bash
yarn add @nucypher/taco
```

For this guide we'll need a few extra packages:

```bash
yarn add ethers@5.7.2 @metamask/detect-provider
```

In order to use `taco`, we have to call `initialize` method first. This method takes care of initializing the WASM module for `taco` dependencies.

```typescript
import { initialize } from '@nucypher/taco';

await initialize();
```

With this out of the way, we're ready to use `taco` in our app.

## 2. Describe decryption conditions

Before we encrypt our data, we have to describe the decryption _conditions_.

_Conditions_ are the requirements for a data recipient to access the plaintext data – i.e. what they will need to prove later to gain decryption rights. There are multiple [`Condition` types](../conditions/conditions/) we can use here, including predefined conditions such as `ERC721Ownership.`

The `ERC721Ownership` condition checks the owner of a given token ID. It can be customized by using the `ownerOf` contract method and comparing it with the requestor's signature. For more information, see the [References](../references.md) section.

We will now specify the conditions that must be met to access the data. In this tutorial, we will require that the requester owns an ERC721 token with a token ID of 5954.

```javascript
import { conditions } from '@nucypher/taco';

const ownsNFT = new conditions.predefined.erc721.ERC721Ownership({
  contractAddress: '0xBC4CA0EdA7647A8aB7C2061c2E118A18a936f13D',
  chain: 80001,
  parameters: [5954], // We need to specify a token ID here
});
```

We create more complex conditions by combining them with `CompoundCondition`

```javascript
import { conditions } from '@nucypher/taco';

const conditions = new conditions.compound.CompoundCondition({
  operator: 'and'
  operands: [
    NFTOwnership,
    // Other conditions can be added here
  ]
});
```

## 3. Encrypt the plaintext

We're ready to encrypt our plaintext and gate access to the encrypted contents with our conditions, `NFTOwnership`.

```javascript
import { encrypt, domains } from '@nucypher/taco';
import { ethers } from "ethers";

const web3Provider = new ethers.providers.Web3Provider(window.ethereum);

const messageKit = await encrypt(
  web3Provider,
  domains.TESTNET,
  message,
  ownsNFT,
  ritualId,
  web3Provider.getSigner() 
);
```

The resulting `messageKit` contains the encrypted data and associated conditions.

## 4. Request decryption rights

Finally, we will test the conditional access control service by requesting decryption rights:

```javascript
import { decrypt, domains, getPorterUri } from '@nucypher/taco';
import { ethers } from "ethers";

const web3Provider = new ethers.providers.Web3Provider(window.ethereum); 

const decryptedMessage = await decrypt(
  web3Provider,
  domains.TESTNET,
  messageKit,
  getPorterUri(domains.TESTNET),
  web3Provider.getSigner()
);

```

At decryption time, the requester will be asked to verify their address by signing a message in their wallet. If the requester's address controls the minimum number (or greater) of the specified NFT, they are eligible to receive the requisite number of decryption fragments. By assembling these fragments, they are able to decrypt and view the plaintext encrypted in the previous step.&#x20;

{% hint style="info" %}
Note that the requester does not need to manually sign the next time they seek access to the data, as their client can temporarily cache their signature. Fresh plaintexts encrypted under any conditions involving the same wallet address are automatically accessible to any requester who has signed at least once, provided they still fulfill the (new) conditions, and the cached signature has not expired.&#x20;
{% endhint %}

## Complete example

This is the complete, end-to-end example of `taco` integration

```typescript
import { initialize, encrypt, decrypt, conditions, domains } from '@nucypher/taco'
import { ethers } from "ethers";

// The data encryptor runs this part
const doEncrypt = async (message) => {
    // We have to initialize TACo library first
    await initialize();

    const web3Provider = new ethers.providers.Web3Provider(window.ethereum);
    const ownsNFT = new conditions.predefined.erc721.ERC721Ownership({
        contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
        parameters: [3591],
        chain: 5,
    });
    const messageKit = await encrypt(
        web3Provider,
        domains.TESTNET,
        message,
        ownsNFT,
        ritualId,
        web3Provider.getSigner()
    )
    return messageKi

// The data recipient runs this part
const doDecrypt = async (messageKit) => {
    // We have to initialize TACo library first
    await initialize();

    const web3Provider = new ethers.providers.Web3Provider(window.ethereum);
    const decryptedMessage = await decrypt(
        web3Provider,
        domains.TESTNET,
        messageKit,
        getPorterUri(domains.TESTNET),
        web3Provider.getSigner()
    );
    return decryptedMessage;
};

const message = 'my secret message';
const encryptedMessage = await doEncrypt(message);
const decryptedMessage = await doDecrypt(encryptedMessage);

if (decryptedMessage === toBytes(message)) {
  console.log('Success!');
}
```

### Example applications

The following samples showcase integrations with React-based web apps, and serve as an 'end-to-end' reference for creating conditions-based encryption & decryption:

* [`nucypher-ts/demos`](https://github.com/nucypher/nucypher-ts/tree/main/demos)
* [`nucypher-ts/examples/taco`](https://github.com/nucypher/nucypher-ts/tree/main/examples/taco)
* [`nucypher/alpha-leaks-demo`](https://github.com/nucypher/alpha-leaks-demo)

