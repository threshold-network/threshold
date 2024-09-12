---
description: >-
  Apart from the opinionated initialization functions, the SDK provides a
  flexible TBTC.initializeCustom function for advanced users.
---

# Custom mode

This function allows setting up the SDK to work with custom tBTC smart contracts and custom Bitcoin network. For example, it can be used to address the following use cases:

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



