# Unmint and redeem

Another core feature of the tBTC bridge is unminting (burning) TBTC tokens and redeeming BTC in return. This guide explains how to perform this process using the SDK.&#x20;

### Initialize the SDK

First, initialize the SDK, as described in the [initialize-sdk.md](initialize-sdk.md "mention") guide.

### Request redemption

Once the SDK is initialized, you can unmint and request for redemption in the following way:

```typescript
import { TBTC } from "@keep-network/tbtc-v2.ts"
import { BigNumber } from "ethers"

// Initialized SDK.
const sdk: TBTC

// Set the P2WPKH/P2PKH or P2WSH/P2SH Bitcoin redeemer address. This is the
// address where redeemed BTC will land.
const bitcoinRedeemerAddress: string = "..."

// Set the desired redemption amount using 1e18 precision. No need to do an 
// explicit approval on the TBTC token upfront.
const amount = BigNumber.from(5 * 1e18)

// Request redemption. This action will burn TBTC tokens and register a
// redemption request in the bridge. Returns the hash of the request redemption
// transaction and the target wallet public key that will handle the redemption.
const {
  targetChainTxHash,
  walletPublicKey
} = await sdk.redemptions.requestRedemption(bitcoinRedeemerAddress, amount)
```
