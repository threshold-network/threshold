# Condition Context and Context Variables

In this section, we introduce the `ConditionContext`, a container for dynamic values to be used in conditions. We call those values "context variables" and can recognize them by the `:` prefix. They act as a placeholder to be specified at the encryption time and filled at the decryption time.

Let's take a look at this `ContractConditon` example:

```typescript
import { conditions } from '@nucypher/taco';

const ownsNFTRaw = new conditions.contract.ContractCondition({
  method: 'balanceOf',
  parameters: [':userAddress'], // <- A context parameter
  standardContractType: 'ERC721',
  contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
  chain: 1,
  returnValueTest: {
    comparator: '>',
    value: ':selectedBalance', // <- Another one
  },
});
```

In this example, we can see two different context variables

* `:userAddress` - A reserved context variable
* `:selectedBalance` - A custom context variable

We can see that there are two types of context variables

* Reserved context variables, that are used by `taco` automatically
* Custom context variables, that have to be filled manually by the decryptor

In order to replace the `:userAddress` context variable with an actual user address, `taco` will query the user to sign `EIP712` message to confirm wallet ownership at the decryption time.

The `:selectedBalance` custom context variable has to be provided to the `decrypt` function by the decryptor:

```typescript
import { decrypt, conditions } from '@nucypher/taco';

const customParameters: Record<string, conditions.CustomContextParam> = {
  ':selectedBalance': 2,
};

const messageKit = await encrypt(
  web3Provider,
  domains.MAINNET,
  message,
  ownsNFT,
  ritualId,
  web3Provider.getSigner(),
  customParameters
);
```

With those context parameters, our conditions is transformed at decryption time into:

```json
{
  method: 'balanceOf',
  parameters: ['0x...'], // The address of the user
  standardContractType: 'ERC721',
  contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
  chain: 1,
  returnValueTest: {
    comparator: '>',
    value: 2, // Concrete value to be used in comparison
  },
}
```

{% hint style="info" %}
This is a contrived example. Time should be taken to think through the use of **custom context variables** since these are provided by the user and can be set to any value. In this case the `selectedBalance`  value can simply be set to `-1` by the user which would grant them access without owning any NFT.\
\
There is a place for custom context variables e.g. providing a merkle tree root as a parameter to a contract function, but these type of examples would be overly complex.
{% endhint %}

### Learn more&#x20;

* [references.md](../references.md "mention")
* [advanced-usage](../advanced-usage/ "mention")
