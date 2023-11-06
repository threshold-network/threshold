# Initialize SDK

Before initializing the tBTC SDK instance in your project, you need to answer the following questions:

* What Bitcoin network and which tBTC contracts do you plan to interact with?
* Do you want to perform just read-only actions or send transactions as well?

Answering those questions will allow initializing the SDK in the right way. The SDK is prepared to handle common use cases but provides some flexibility as well. This guide explains that in detail.

### Ethereum and Bitcoin mainnet

This is the most common case when you want to use the SDK to interact with the tBTC bridge on Ethereum and Bitcoin mainnet. The SDK addresses it by exposing the `TBTC.initializeMainnet` function. That function:

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

### Ethereum and Bitcoin testnet

This is the case where you want to use the SDK to interact with the tBTC bridge on Ethereum and Bitcoin testnet. The SDK addresses it by exposing the `TBTC.initializeGoerli` function. That function:

* Takes an Ethers signer or provider as a parameter and uses it to interact with tBTC contracts deployed on Ethereum Goerli
* Automatically configures an Electrum Bitcoin client to communicate with the Bitcoin testnet (communication is done through a set of pre-configured Electrum servers)

The usage of this function is exactly the same as for the previous `TBTC.initializeMainnet` function.

{% hint style="warning" %}
Ethereum Goerli shutdown is planned for the end of 2023. Because of that, the tBTC bridge is switching to Sepolia. That switch will be reflected in the SDK soon.
{% endhint %}

### Custom mode

Apart from the _opinionated_ initialization functions, the SDK provides a flexible `TBTC.initializeCustom` function for advanced users. This function allows setting up the SDK to work with custom tBTC smart contracts and custom Bitcoin network. For example, it can be used to address the following use cases:

#### Using locally deployed tBTC contracts and local Bitcoin network

This is the common case when you are setting up a development environment. Let's suppose you have deployed the tBTC contracts on your local Ethereum chain and you are using a [local Bitcoin regtest node](https://developer.bitcoin.org/examples/testing.html#regtest-mode). The following snippet demonstrates SDK initialization in that case:

```typescript
import * as ethers from "ethers"
import {
  TBTC,
  TBTCContracts,
  EthereumBridge,
  EthereumTBTCToken,
  EthereumTBTCVault,
  EthereumWalletRegistry,
  ElectrumClient,
} from "@keep-network/tbtc-v2.ts"

// Create an Ethers provider. Pass the URL of your local Ethereum node.
const provider = new ethers.providers.JsonRpcProvider("...")
// Create an Ethers signer. Pass the private key and the above provider.
const signer = new ethers.Wallet("...", provider)

// Create a reference to your locally deployed tBTC contracts.
// ABI will be loaded from the following contract packages:
// - @keep-network/tbtc-v2
// - @keep-network/ecdsa
const tbtcContracts: TBTCContracts = {
  bridge: new EthereumBridge({address: "...", signerOrProvider: signer}),
  tbtcToken: new EthereumTBTCToken({address: "...", signerOrProvider: signer}),
  tbtcVault: new EthereumTBTCVault({address: "...", signerOrProvider: signer}),
  walletRegistry: new EthereumWalletRegistry({address: "...", signerOrProvider: signer})
}

// Create an Electrum Bitcoin client pointing to your local regtest node.
const bitcoinClient = ElectrumClient.fromUrl("...")

// Initialize the SDK.
const sdk = await TBTC.initializeCustom(tbtcContracts, bitcoinClient)
```

#### Using a custom Bitcoin client instead of the automatically configured one

In some cases, you may want to initialize the SDK for mainnet/testnet, but point the Electrum Bitcoin client to your own server. Here is how you can do that:

```typescript
import * as ethers from "ethers"
import {
  TBTC,
  TBTCContracts,
  loadEthereumContracts,
  ElectrumClient
} from "@keep-network/tbtc-v2.ts"

// Create an Ethers provider. Pass a URL of an Ethereum mainnet node.
// For example, Alchemy or Infura.
const provider = new ethers.providers.JsonRpcProvider("...")
// Create an Ethers signer. Pass the private key and the above provider.
const signer = new ethers.Wallet("...", provider)

// Load tBTC Ethereum mainnet contracts manually.
const tbtcContracts: TBTCContracts = await loadEthereumContracts(signer, "mainnet")

// Create an Electrum Bitcoin client pointing to your own mainnet server.
const bitcoinClient = ElectrumClient.fromUrl("...")

// Initialize the SDK with Electrum Bitcoin client pointing to your own server.
const sdk = await TBTC.initializeCustom(tbtcContracts, bitcoinClient)
```

You can even use another (non-Electrum) Bitcoin client implementation. This is how you can achieve that:

```typescript
import * as ethers from "ethers"
import {
  TBTC,
  TBTCContracts,
  loadEthereumContracts,
  BitcoinClient,
} from "@keep-network/tbtc-v2.ts"

// Create an Ethers provider. Pass a URL of an Ethereum mainnet node.
// For example, Alchemy or Infura.
const provider = new ethers.providers.JsonRpcProvider("...")
// Create an Ethers signer. Pass the private key and the above provider.
const signer = new ethers.Wallet("...", provider)

// Load tBTC Ethereum mainnet contracts manually.
const tbtcContracts: TBTCContracts = await loadEthereumContracts(signer, "mainnet")

// Create your own Bitcoin client implementation.
class OwnBitcoinClientImpl implements BitcoinClient {
  // Implement all required methods
}

// Initialize your own Bitcoin client implementation.
const bitcoinClient = new OwnBitcoinClientImpl()

// Initialize the SDK with your own Bitcoin client implementation.
const sdk = await TBTC.initializeCustom(tbtcContracts, bitcoinClient)
```

#### Using mock tBTC contracts and mock Bitcoin network

Sometimes, you may also want to initialize the SDK with mock tBTC contracts and Bitcoin network for testing purposes. This can be done as follows:

```typescript
import {
  TBTC,
  TBTCContracts,
  Bridge,
  TBTCToken,
  TBTCVault,
  WalletRegistry,
  BitcoinClient,
} from "@keep-network/tbtc-v2.ts"

// Create mock Bridge contract implementation.
class MockBridge implements Bridge {
  // Implement all required methods
}

// Create mock TBTCToken contract implementation.
class MockTBTCToken implements TBTCToken {
  // Implement all required methods
}

// Create mock TBTCVault contract implementation.
class MockTBTCVault implements TBTCVault {
  // Implement all required methods
}

// Create mock WalletRegistry contract implementation.
class MockWalletRegistry implements WalletRegistry {
  // Implement all required methods
}

// Create mock BitcoinClient contract implementation.
class MockBitcoinClient implements BitcoinClient {
  // Implement all required methods
}

// Create instances of the mock contracts.
const tbtcContracts: TBTCContracts = {
  bridge: new MockBridge(),
  tbtcToken: new MockTBTCToken(),
  tbtcVault: new MockTBTCVault(),
  walletRegistry: new MockWalletRegistry()
}

// Create an instance of the mock Bitcoin client.
const bitcoinClient = new MockBitcoinClient()

// Initialize the SDK.
const sdk = await TBTC.initializeCustom(tbtcContracts, bitcoinClient)
```





