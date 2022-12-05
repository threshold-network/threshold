# Get Started with TAC

The following interactive tutorial is a quick way for developers to familiarize themselves with the Threshold Access Control service. Before jumping into the following, we recommend reading this introduction to [conditions-based-decryption-cbd.md](../../fundamentals/threshold-access-control/conditions-based-decryption-cbd.md "mention") and its key concepts. CBD is the primary technology underpinning Threshold Access Control and directly enables data sharing predicated on the fulfillment of predefined conditions.

## 1. Install nucypher-ts

We'll kick things off by installing `nucypher-ts` – a TypeScript library for interacting with access control functionality in the browser. The APIs for leveraging most TAC functionality are contained in nucypher-ts.

```
yarn add @nucypher/nucypher-ts@alpha
```

Also, for this example we will need some extra packages:

```
yarn add ethers @metamask/detect-provider
```

{% hint style="warning" %}
`nucypher-ts` is under [active development](https://github.com/nucypher/nucypher-ts/pulls).
{% endhint %}

## 2. Build a Cohort

Next, we'll parametrize a _Cohort_ to correspond to our risk preferences. Cohort objects delineate the group of independent nodes that will collectively provide access control service to a given data sharing flow. _Threshold_ and _Shares_ are two parameters used to construct a Cohort. For example, a `3-of-5` Cohort requires responses – the delivery of shares – from a minimum of 3 out of a total of 5 Cohort members in order to reconstruct the original plaintext data.

We generate a Cohort object by:

```javascript
import { Cohort } from '@nucypher/nucypher-ts';

const config = {
  threshold: 3,
  shares: 5,
  porterUri: 'https://porter-tapir.nucypher.community',
};
const newCohort = await Cohort.create(config);
```

Notice that we have also provided a `porterUri`. [**Porter**](https://github.com/nucypher/nucypher-porter) **is a web-based service that interacts with nodes on the network on behalf of applications – an "Infura for TAC".** In this example, we've chosen an tapir (testnet) Porter endpoint.

## 3. Create Conditions

We will now specify the conditions on which data access will be predicated – i.e. what will the data requester need to prove in order to gain decryption rights. In this tutorial, nodes will check that the requester owns an ERC721 NFT with a token ID of `5954`.

```javascript
import { Conditions } from '@nucypher/nucypher-ts';

const NFTOwnership = new Conditions.ERC721Ownership({
  contractAddress: '0xBC4CA0EdA7647A8aB7C2061c2E118A18a936f13D',
  chain: 5, // Tapir network uses Görli testnet
  parameters: [5954],
});
```

ERC721Ownership is provided for convenience. The full underlying configuration can be viewed [here](https://docs.threshold.network/app-development/threshold-access-control-tac/references/conditions#conditions.erc721ownership).

```javascript
import { Conditions, ConditionSet } from '@nucypher/nucypher-ts';

const conditions = new ConditionSet([NFTOwnership]);
```

This tutorial will only specify a single Condition to access the data. &#x20;

{% hint style="info" %}
There is support for a variety of [Condition types](references/conditions.md) and more granular configurations. It is also possible to logically compose and combine multiple conditions into a single overall condition via [ConditionSet.](references/condition-set.md)
{% endhint %}

## 4. Build a Strategy

We now bundle the Cohort, ConditionSet, and any other extra parameters into a [_Strategy_](references/strategy.md)_:_

```javascript
import { Strategy } from '@nucypher/nucypher-ts';

const newStrategy = Strategy.create(
  newCohort,
  conditions
);
```

Next, we deploy this Strategy to the Threshold Network:

```typescript
import detectEthereumProvider from '@metamask/detect-provider';
import providers from 'ethers';

const MMprovider = await detectEthereumProvider();
const rinkeby = providers.providers.getNetwork('Rinkeby');

if (MMprovider) {
  const web3Provider = new providers.providers.Web3Provider(
    MMprovider,
    rinkeby
  );
  const newDeployed = await newStrategy.deploy('test', web3Provider);
} 
```

{% hint style="info" %}
Deploying a Strategy requires writing to the blockchain. This requires a funded wallet and connection to the blockchain via a `provider`(e.g. MetaMask).
{% endhint %}

## 5. Encrypt the plaintext

We're now able to encrypt data to this newly deployed Strategy – which implies future access to this data will be based on ownership of the previously specified NFT, and nothing else. We'll now encrypt a plaintext using the encryptor object:

```javascript
const encrypter = newDeployed.encrypter;

const plaintext = 'this is a secret';
const encryptedMessageKit = encrypter.encryptMessage(plaintext);
```

## 6. Request decryption rights

Finally, we'll test the access control service by submitting a request to the network:

```javascript
const decrypter = newDeployed.decrypter;

const decryptedMessage = await decrypter.retrieveAndDecrypt([
  encryptedMessageKit,
]);
```

At decryption time, the requester will be asked to verify their address by signing a message in MetaMask. If they own the correct NFT, the message will decrypt successfully.

For more guidance on Cohort, Condition and Strategy object reuse and customization, check out the [References](references/) page.&#x20;
