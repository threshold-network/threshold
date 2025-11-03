# Quickstart

Here you can find instructions explaining how to use the SDK in your own project.

### Installation

To install the tBTC SDK in your project, run:

{% tabs %}
{% tab title="yarn" %}
{% code fullWidth="false" %}
```bash
yarn add @keep-network/tbtc-v2.ts
```
{% endcode %}
{% endtab %}

{% tab title="npm" %}
```bash
npm i @keep-network/tbtc-v2.ts
```
{% endtab %}
{% endtabs %}

Please note that you will also need to install the [ethers v5](https://docs.ethers.org/v5) library to initialize a signer or provider. To do so, invoke:

{% tabs %}
{% tab title="yarn" %}
```bash
yarn add ethers@legacy-v5
```
{% endtab %}

{% tab title="npm" %}
```bash
npm i ethers@legacy-v5
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
```
The SDK depends on ethers v5. Proper support for newer ethers versions is 
not guaranteed right now.
```
{% endhint %}

### Usage

Here is a brief example demonstrating the use of the SDK in Ethereum:

```typescript
// Import SDK entrypoint component.
import { TBTC } from "@keep-network/tbtc-v2.ts"

// Create an instance of ethers signer.
const signer = (...)

// Initialize the SDK for Ethereum only.
const sdk = await TBTC.initializeMainnet(signer)

// Access SDK features.
sdk.deposits.(...)
sdk.redemptions.(...)

// Access tBTC smart contracts directly.
sdk.tbtcContracts.(...)

// Access Bitcoin client directly.
sdk.bitcoinClient.(...)
```

#### Crosschain

Here is a brief example demonstrating the use of the SDK in some L2, e.g. Arbitrum:

```typescript
// Import SDK entrypoint component.
import { TBTC } from "@keep-network/tbtc-v2.ts"

// Create an instance of ethers provider.
const ethProvider = (...)

// Create an instance of ethers signer.
const arbitrumSigner = (...)

// Initialize the SDK for Ethereum only.
const sdk = await TBTC.initializeMainnet(ethProvider, true)

// Initialize it for any L2 (E.g., Arbitrum)
await sdk.initializeCrossChain("Arbitrum", arbitrumSigner);

// Access SDK features.
sdk.deposits.(...)
sdk.redemptions.(...)

// Access tBTC smart contracts directly.
sdk.tbtcContracts.(...)

// Access Bitcoin client directly.
sdk.bitcoinClient.(...)
```
