---
description: >-
  This is the case where you want to use the SDK to interact with the tBTC
  bridge on Ethereum and Bitcoin testnet.
---

# Ethereum and Bitcoin testnet

&#x20;The SDK addresses it by exposing the `TBTC.initializeSepolia` function. That function:

* Takes an Ethers signer or provider as a parameter and uses it to interact with tBTC contracts deployed on Ethereum Sepolia
* Automatically configures an Electrum Bitcoin client to communicate with the Bitcoin testnet (communication is done through a set of pre-configured Electrum servers)

The usage of this function is exactly the same as for the previous `TBTC.initializeMainnet` function.
