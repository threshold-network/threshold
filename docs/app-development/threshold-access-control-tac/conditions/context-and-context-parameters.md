# Context and Context Parameters

In this section, we introduce the `ConditionContext`, a container for parameters to be used in conditions. We call those parameters "context parameters" and can recognize them by the `:` prefix. They act as a template to be specified at the encryption time and filled at the decryption time.

Let's take a look at this `ContractConditon` example:

```typescript
import { conditions } from '@nucypher/taco';

const ownsNFTRaw = new conditions.base.ContractCondition({
  method: 'balanceOf',
  parameters: [':userAddress'], // <- A context parameter
  standardContractType: 'ERC721',
  contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
  chain: 5,
  returnValueTest: {
    comparator: '>',
    value: ':selectedBalance', // <- Another one
  },
});
```

In this example, we can see two different context parameters

* `':userAddress'` - A reserved context parameter
* `':selectedBalance'` - A custom context parameter

We can see that there are two types of context parameters

* Reserved parameters, that are used by `taco` automatically
* Custom parameters, that have to be filled manually by the decryptor

In order to replace `:userAddress'` parameter with an actual decrypter address, `taco` will query the user to sign `EIP712` message to confirm wallet ownership at the decryption time.

The `':selectedBalance'` custom context parameter has to be provided to the `decrypt` function by the decryptor:

```typescript
import { decrypt, conditions } from '@nucypher/taco';

const customParameters: Record<string, conditions.CustomContextParam> = {
  ':selectedBalance': 2,
};

const messageKit = await encrypt(
  web3Provider,
  domains.TESTNET,
  message,
  ownsNFT,
  ritualId,
  web3Provider.getSigner(),
  customParameters
);
```

With those context parameters, our conditions is transformed at decryption time into:

```
{
  method: 'balanceOf',
  parameters: ['0x...0'], // The address of the decryptor
  standardContractType: 'ERC721',
  contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
  chain: 5,
  returnValueTest: {
    comparator: '>',
    value: 2, // Concrete value to be used in comparison
  },
}
```

### Learn more&#x20;

* [references.md](../references.md "mention")
* [advanced-usage](../advanced-usage/ "mention")
