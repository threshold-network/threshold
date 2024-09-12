---
description: >-
  This mode of the SDK will initialize Ethereum, Bitcoin and an L2 to work with
  tBTC. Compatible with mainnet and testnet.
---

# Crosschain

{% hint style="info" %}
Currently only functional with **Arbitrum**, soon more blockchains will be available.
{% endhint %}

## Mainnet

The SDK addresses it by exposing the `TBTC.initializeMainnet` function. That function:

* Takes an Ethers signer or provider as a parameter and uses it to interact with tBTC contracts deployed on Ethereum mainnet
* Needs to receive as a second parameter the value `true` to activate the crosschain mode.
* Automatically configures an Electrum Bitcoin client to communicate with the Bitcoin mainnet (communication is done through a set of pre-configured Electrum servers)

The following code snippet presents the usage of this function:

```typescript
import * as ethers from "ethers"
import { TBTC } from "@keep-network/tbtc-v2.ts"

// Create an Ethers provider. Pass the URL of an Ethereum and Arbitrum mainnet node.
// For example, Alchemy or Infura.
const ethProvider = new ethers.providers.JsonRpcProvider("...")
const arbProvider = new ethers.providers.JsonRpcProvider("...")

// Create an Ethers signer. Pass the private key and the above provider.
const arbSigner = new ethers.Wallet("...", provider)

// If you want to initialize the SDK, it is enough to pass the provider.
const sdk = await TBTC.initializeMainnet(ethProvider, true)

// Initialize cross-chain functionality for TBTC on Arbitrum
await sdk.initializeCrossChain("Arbitrum", arbSigner);

// The SDK will be ready to launch read and write in Arbitrum. 
```

{% hint style="info" %}
The above code snippet presents just one way of creating an Ethers signer/provider. Please refer [Ethers v5 documentation](https://docs.ethers.org/v5/api) to learn more.
{% endhint %}



## Testnet

The SDK addresses it by exposing the `TBTC.initializeSepolia` function. That function:

* Takes an Ethers signer or provider as a parameter and uses it to interact with tBTC contracts deployed on Ethereum Sepolia
* Needs to receive as a second parameter the value `true` to activate the crosschain mode.
* Automatically configures an Electrum Bitcoin client to communicate with the Bitcoin testnet (communication is done through a set of pre-configured Electrum servers)

The usage of this function is exactly the same as for the previous `TBTC.initializeMainnet` function.

```typescript
import * as ethers from "ethers"
import { TBTC } from "@keep-network/tbtc-v2.ts"

// Create an Ethers provider. Pass the URL of an Ethereum and Arbitrum testnet node.
// For example, Alchemy or Infura.
const ethProvider = new ethers.providers.JsonRpcProvider("...")
const arbProvider = new ethers.providers.JsonRpcProvider("...")

// Create an Ethers signer. Pass the private key and the above provider.
const arbSigner = new ethers.Wallet("...", provider)

// If you want to initialize the SDK, it is enough to pass the provider.
const sdk = await TBTC.initializeSepolia(ethProvider, true)

// Initialize cross-chain functionality for TBTC on Arbitrum
await sdk.initializeCrossChain("Arbitrum", arbSigner);

// The SDK will be ready to launch read and write in Arbitrum. 
```
