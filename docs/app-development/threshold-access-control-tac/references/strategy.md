# Strategy

A Strategy combines all possible configuration parameters for using CBD. It takes the following parameters:

* `cohort` - a `Cohort` object
* `conditionSet?` - an optional `ConditionSet`. If used, all encryptions made via this strategy have a default Condition Set assigned
* `aliceSecretKey?` - an optional Secret Key for the encrypter
* `bobSecretKey?` - an optional SecretKey for decrypter

If the optional secret keys are not provided, new ones will be generated instead.

## Create a Strategy

Assuming we have a Cohort already defined, we can construct a Strategy:

```javascript
import { Cohort, Strategy } from '@nucypher/nucypher-ts';

const config = {
  threshold: 3,
  shares: 5,
  porterUri: 'https://porter-ibex.nucypher.community',
};
const newCohort = await Cohort.create(config);

const newStrategy = Strategy.create(
  newCohort
);
```

## Deploy a Strategy

Before we can encrypt/decrypt, the Threshold network needs to be made aware of our Strategy. We do this by deploying:

```javascript
import detectEthereumProvider from '@metamask/detect-provider';
import providers from 'ethers';

const MMprovider = await detectEthereumProvider();
const mumbai = providers.providers.getNetwork(80001);

if (MMprovider) {
  const web3Provider = new providers.providers.Web3Provider(
    MMprovider,
    mumbai
  );
  const newDeployed = await newStrategy.deploy('test', web3Provider);
}
```

`Strategy.deploy` takes 2 parameters:

* `label` - this is a string that the network uses to identify the strategy
* `provider` - deploying a Strategy requires writing to the smart contract, so a connection to a wallet is required via a Web3 provider

Deploying a strategy returns a new `DeployedStrategy` object. This object grants us access to the `encrypter` and `decrypter` which can then be used throughout an application.

```javascript
const encrypter = newDeployed.encrypter;
const decrypter = newDeployed.decrypter;

const plaintext = 'this is a secret';
const encryptedMessageKit = encrypter.encryptMessage(plaintext);

const decryptedMessage = await decrypter.retrieveAndDecrypt([
  encryptedMessageKit,
]);
```

## Import and Export Strategies

Strategies can be exported allowing them to be reused easily. The syntax is the same whether the strategy has been deployed or not.

```javascript
import { DeployedStrategy } from '@nucypher/nucypher-ts';

const configJSON = newDeployed.toJSON();
console.log(configJSON);
/*
LARGE JSON OBJECT
*/
const importedStrategy = DeployedStrategy.fromJSON(configJSON);
```

Similarly, we can import and export the decrypter objects to JSON. This allows us to rebuild the decrypter on a client facing application:

```javascript
import { DeployedStrategy } from '@nucypher/nucypher-ts';

const decrypter = newDeployed.decrypter;
const decrypterJSON = decrypter.toJSON();
// save this JSON are send it over a side channel to a client facing app

// on the client app
import { tDecDecrypter } from '@nucypher/nucypher-ts';
const newDecrypter = tDecDecrypter.fromJSON(decrypterJSON);
```
