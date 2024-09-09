# TACo Integration

Note that using TACo in production requires a unique DKG initialization ritual, DKG public key and cohort of nodes running TACo software – these are accessed via a unique `ritualID` parameter in the third step below. Please see the [beta program](../integration-guide/mainnet-beta-program.md) page for instructions on how to initialize a DKG ritual.

## 0. Pick an appropriate `taco` version

Before we install `taco`, we need to consider which domain we would like to use:

* `MAINNET` (`mainnet` network) - production environment
* `TESTNET` (`tapir` network) - stable testnet that matches `mainnet`
* `DEVNET` (`lynx` network) - bleeding-edge testnet used for internal development and future features

{% hint style="info" %}
`tapir` is the stable network recommended for developers.
{% endhint %}

Once you've picked a network, install `@nucypher/taco` from [npm.js](https://www.npmjs.com/package/@nucypher/taco?activeTab=versions) with the appropriate tag based on the chosen network. To find the appropriate version, refer to the "Tags" column in the "Current Tags" section.

You can learn more about the current state of `mainnet` and test networks here:

* [mainnet-beta-program.md](../integration-guide/mainnet-beta-program.md "mention")
* [get-started-with-tac.md](get-started-with-tac.md "mention")

## 1. Install and integrate `taco`

To begin, we need to install the `taco` , and `taco-auth`libraries:

```bash
yarn add @nucypher/taco @nucypher/taco-auth
```

For this guide, we'll need a few extra packages:

```bash
yarn add ethers@5.7.2 @metamask/detect-provider
```

To use `taco`, we have to call `initialize` method first. This method takes care of initializing the WASM module for `taco` dependencies.

```typescript
import { initialize } from '@nucypher/taco';

await initialize();
```

With this out of the way, we're ready to use `taco` in our app.

## 2. Define decryption conditions

Before we encrypt our data, we have to define the decryption _conditions_.

_Conditions_ are the requirements for a data recipient to access the plaintext data – i.e. what they will need to prove later to gain decryption rights. There are multiple [`Condition` types](../conditions/conditions/) we can use here, including predefined conditions such as `ERC721Ownership.`

The `ERC721Ownership` condition checks the owner of a given token ID. It can be customized by using the `ownerOf` contract method and comparing it with the requestor's signature. For more information, see the [References](../references.md) section.

We will now specify the condition that must be met to access the data. In this tutorial, we will require that the requester owns an ERC721 token on Ethereum mainnet with a token ID of `5954`.

```typescript
import { conditions } from '@nucypher/taco';

const ownsNFT = new conditions.predefined.erc721.ERC721Ownership({
  contractAddress: '0xBC4CA0EdA7647A8aB7C2061c2E118A18a936f13D',
  chain: 1,
  parameters: [5954], // We need to specify a token ID here
});
```

We can create more complex conditions by combining them with `CompoundCondition`

```typescript
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
  domains.MAINNET,
  message,
  ownsNFT,
  ritualId,
  web3Provider.getSigner() 
);
```

The resulting `messageKit` contains the encrypted data and associated conditions.

## 4. Request decryption

Finally, we will test the conditional access control service by requesting decryption:

```typescript
import { conditions, decrypt, domains } from '@nucypher/taco';
import { EIP4361AuthProvider, USER_ADDRESS_PARAM_DEFAULT } from '@nucypher/taco-auth';
import { ethers } from "ethers";

const web3Provider = new ethers.providers.Web3Provider(window.ethereum); 

const conditionContext =
  conditions.context.ConditionContext.fromMessageKit(messageKit);
  
// auth provider when condition contains ":userAddress" context variable
// the decryptor user must provide a signature to prove ownership of the wallet address
const authProvider = new EIP4361AuthProvider(
    web3Provider,
    web3Provider.getSigner(),
);
conditionContext.addAuthProvider(USER_ADDRESS_PARAM_DEFAULT, authProvider);

const decryptedMessage = await decrypt(
    web3Provider,
    domains.MAINNET,
    messageKit,
    conditionContext,
);
```

At decryption time, the requester will be asked to verify their address by signing a message from their wallet. If the requester's address controls the minimum number (or greater) of the specified NFT, they are eligible to receive the requisite number of decryption fragments. By assembling these fragments, they can decrypt the encrypted data and view the plaintext.&#x20;

{% hint style="info" %}
Note that the requester does not need to manually sign the next time they seek access to the data, as the `taco` client will cache their signature. Fresh plaintexts encrypted under any conditions involving the same wallet address are automatically accessible to any requester who has signed at least once, provided they still fulfill any requisite conditions, and the cached signature has not expired.&#x20;
{% endhint %}

## Complete example

This is the complete, end-to-end example of `taco` integration

```typescript
import { encrypt, decrypt, conditions, domains, initialize  } from '@nucypher/taco';
import { EIP4361AuthProvider, USER_ADDRESS_PARAM_DEFAULT } from '@nucypher/taco-auth';
import { ethers } from "ethers";

// The data encryptor runs this part
const doEncrypt = async (message) => {
  const web3Provider = new ethers.providers.Web3Provider(window.ethereum);
  const ownsNFT = new conditions.predefined.erc721.ERC721Ownership({
      contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
      parameters: [3591],
      chain: 1,
  });
  const messageKit = await encrypt(
      web3Provider,
      domains.MAINNET,
      message,
      ownsNFT,
      ritualId,
      web3Provider.getSigner()
  )
  return messageKit

// The data recipient runs this part
const doDecrypt = async (messageKit) => {
  const web3Provider = new ethers.providers.Web3Provider(window.ethereum);
  
  const conditionContext =
      conditions.context.ConditionContext.fromMessageKit(messageKit);

  // auth provider when condition contains ":userAddress" context variable
  const authProvider = new EIP4361AuthProvider(
      web3Provider,
      web3Provider.getSigner(),
  );
  conditionContext.addAuthProvider(USER_ADDRESS_PARAM_DEFAULT, authProvider);
    
  const decryptedMessage = await decrypt(
      web3Provider,
      domains.MAINNET,
      messageKit,
      conditionContext,
  );
  return decryptedMessage;
};

// We have to initialize TACo library first
await initialize();
  
const message = 'my secret message';
const encryptedMessage = await doEncrypt(message);
const decryptedMessage = await doDecrypt(encryptedMessage);

if (decryptedMessage === toBytes(message)) {
  console.log('Success!');
}
```
