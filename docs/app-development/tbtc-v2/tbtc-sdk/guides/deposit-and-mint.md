# Deposit and mint

The main feature of the tBTC bridge is depositing BTC and using it to mint the ERC-20 TBTC token.    This guide explains how to perform this process using the SDK.&#x20;

### Initialize the SDK

First, initialize the SDK, as described in the [initialize-sdk](initialize-sdk/ "mention") guide.

### Initiate the deposit&#x20;

Once the SDK is initialized, you can initiate the deposit and get their Bitcoin address in the following way:

```typescript
import { TBTC } from "@keep-network/tbtc-v2.ts"

// Initialized SDK.
const sdk: TBTC

// Set the P2WPKH/P2PKH Bitcoin recovery address. It can be used to recover
// deposited BTC in case something exceptional happens.
const bitcoinRecoveryAddress: string = "..."

// Initiate the deposit.
const deposit = await sdk.deposits.initiateDeposit(bitcoinRecoveryAddress)

// Take the Bitcoin deposit address. BTC must be sent here.
const bitcoinDepositAddress = await deposit.getBitcoinAddress()
```

### Make the Bitcoin deposit transaction

The next step is doing the actual Bitcoin deposit transaction to the deposit address. There are two possible ways to do so:

* **Recommended**: Use an external Bitcoin wallet&#x20;
* **Non-recommended**: Use the experimental SDK [`DepositFunding`](https://github.com/keep-network/tbtc-v2/blob/f78fe3808733a56cdd5c9e4adcaaf84921cadd3b/typescript/src/services/deposits/funding.ts#L27) component. This code is used for testing purposes and **is not safe** for production use!

### Initiate minting

Once the Bitcoin deposit transaction is done, minting can be initiated. This can be done using the `initiateMinting` method exposed by the `Deposit` class. This method has two modes:

* **Automatic**: Automatically detect the latest funding UTXO and use it to initiate minting. This mode is the default one that should be used when a single Bitcoin deposit transaction has been made.
* **Manual**: Take a funding UTXO as a parameter and use it to initiate minting. This mode can be useful when multiple Bitcoin deposit transactions target the same deposit address.

Here is an example of both:

{% tabs %}
{% tab title="Automatic" %}
```typescript
// Initiate minting using latest funding UTXO. Returns hash of the
// initiate minting transaction. Can throw an error if there are no
// Bitcoin funding transactions targeting this deposit address. 
// In that case, consider retrying after a delay.
const txHash = await deposit.initiateMinting()
```
{% endtab %}

{% tab title="Manual" %}
```typescript
// Detect funding UTXOs manually. There can be more than one.
await fundingUTXOs = await deposit.detectFunding()

// Initiate minting using one of the funding UTXOs. Returns hash of the
// initiate minting transaction.
const txHash = await deposit.initiateMinting(fundingUTXOs[0])
```
{% endtab %}
{% endtabs %}
