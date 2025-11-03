---
description: >-
  This is the most common case when you want to use the SDK to interact with the
  tBTC bridge on Ethereum and Bitcoin mainnet.
---

# Ethereum and Bitcoin mainnet

The SDK addresses it by exposing the `TBTC.initializeMainnet` function. That function:

* Takes an Ethers signer or provider as a parameter and uses it to interact with tBTC contracts deployed on Ethereum mainnet
* Automatically configures an Electrum Bitcoin client to communicate with the Bitcoin mainnet (communication is done through a set of pre-configured Electrum servers)

The following code snippet presents the usage of this function:

```typescript
import * as ethers from "ethers"
import { TBTC } from "@keep-network/tbtc-v2.ts"

// Create an Ethers provider. Pass the URL of an Ethereum mainnet node.
// For example, Alchemy or Infura.
const provider = new ethers.providers.JsonRpcProvider("...")
// Create an Ethers signer. Pass the private key and the above provider.
const signer = new ethers.Wallet("...", provider)

// If you want to initialize the SDK just for read-only actions, it is
// enough to pass the provider. 
const sdkReadonly = await TBTC.initializeMainnet(provider)
// If you want to make transactions as well, you have to pass the signer.
const sdk = await TBTC.initializeMainnet(signer)
```

{% hint style="info" %}
The above code snippet presents just one way of creating an Ethers signer/provider. Please refer [Ethers v5 documentation](https://docs.ethers.org/v5/api) to learn more.
{% endhint %}
