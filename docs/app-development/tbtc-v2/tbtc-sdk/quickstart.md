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

Here is a short example demonstrating SDK usage:

```typescript
// Import SDK entrypoint component.
import { TBTC } from "@keep-network/tbtc-v2.ts"

// Create an instance of ethers signer.
const signer = (...)

// Initialize the SDK.
const sdk = await TBTC.initializeMainnet(signer)

// Access SDK features.
sdk.deposits.(...)
sdk.redemptions.(...)

// Access tBTC smart contracts directly.
sdk.tbtcContracts.(...)

// Access Bitcoin client directly.
sdk.bitcoinClient.(...)
```
