# Condition Hierarchies

[Conditions](../references/conditions.md) can be attached at several steps in the TAC lifecycle, and they have a fixed hierarchy at runtime. This means default Conditions can be specified and subsequently overwritten later on in the process.&#x20;

## Strategy Conditions

Conditions can be attached directly to a [Strategy](../references/strategy.md). They have the lowest precedence and are a great place for including defaults or 'fall back' conditions.

```javascript
import { Cohort, Conditions, ConditionSet, Strategy } from '@nucypher/nucypher-ts';

const config = {
  threshold: 3,
  shares: 5,
  porterUri: 'https://porter-tapir.nucypher.community',
};
const newCohort = await Cohort.create(config);

const NFTOwnership = new Conditions.ERC721Ownership({
  contractAddress: '0xBC4CA0EdA7647A8aB7C2061c2E118A18a936f13D',
  chain: 5, // Tapir network uses Görli testnet
  parameters: [5954],
})
const conditions = new ConditionSet([NFTOwnership]);

const newStrategy = Strategy.create(
  newCohort,
  conditions
);
```

All `encrypter` objects that a deployed Strategy produces will automatically have these conditions included. Therefore, all encrypted messages will require these conditions to be satisfied.

## Encrypter Conditions

This is the next level of precedence in the hierarchy, where each encrypter object can have its own conditions. Assuming the above strategy has been deployed, we can attach conditions in the following way:

```javascript
const encrypter = deployedStrategy.encrypter;

const newNFTOwnership = new Conditions.ERC721Ownership({
  contractAddress: '0xBC4CA0EdA7647A8aB7C2061c2E118A18a936f13D',
  chain: 5,
  parameters: [5000], // let's change the specific NFT
})

encrypter.conditions = new ConditionSet([newNFTOwnership])
```

This will **overwrite** the Strategy conditions we defined above - only the new Conditions will be evaluated, not both. All messages encrypted with `encrypter` will require `newNFTOwnership` to be satisfied.

## Message Conditions

This is the final, and highest priority, Condition type. When encrypting a message, Conditions can be added that apply **only** to this specific encryption. Again, they will overwrite any Conditions specified during Strategy creation or within the encrypter.

```javascript
const NFTBalance = new Conditions.ERC721Balance({
  contractAddress: '0xBC4CA0EdA7647A8aB7C2061c2E118A18a936f13D',
  chain: 5,
});

const plaintext = 'this is a secret';
const encryptedMessageKit = encrypter.encryptMessage(plaintext, new ConditionSet([NFTBalance]));
```

Here we've actually made our Condition more relaxed, and only require a non-zero balance with the NFT contract.
