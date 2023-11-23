# ContractCondition

`ContractConditions` allow us to base access to our data on the evaluation of contract calls.

Below, we can see an example of using  `ContractConditon` to gate access to our data behind an NFT ownership condition.&#x20;

```typescript
import { conditions } from '@nucypher/taco';

const ownsNFT = new conditions.base.ContractCondition({
  method: 'balanceOf',
  parameters: [':userAddress'],
  standardContractType: 'ERC721',
  contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
  chain: 5,
  returnValueTest: {
    index: 0,
    comparator: '>',
    value: 0,
  },
});

```

Now, let's break it down step by step.

With `ContractConditions`, we can either use one of the predefined contracts, such as `ERC20` or `ERC721` standards. We can also use [any arbitrary contract calls provided we include `functionAbi`](../advanced-usage/use-custom-contract-calls.md) into our condition.

In the case of our condition, we use a `standardContractType` of `ERC721`.&#x20;

```typescript
standardContractType: 'ERC721',
```

This contract exposes a number of methods, including `balanceOf`. Knowing the signature of this method, `balanceOf(address)`, we need to pass one parameter into `parameters`

```typescript
 method: 'balanceOf',
 parameters: [':userAddress'],
```

`':userAddress'` is a reserved [context parameter](https://app.gitbook.com/o/R2meumXNNad4y1B10iL7/s/WosjlL4zUGUMlcMfuSAp/\~/changes/274/app-development/threshold-access-control-tac/conditions/context-and-context-parameters) that denotes the address of the recipient that will attempt to decrypt our data. Before the attempt is made, the `':userAddress'` value will be replaced with the actual wallet address.

We can also use a concrete wallet address if we want to grant access to a specific recipient:

```typescript
 parameters: ['0x0000000000000000000000000000000000000000'],
```

Now that we've specified our contract call, we need to figure out what to do with the contract call results. Let's take a look at the `returnValueTest` field:

```typescript
returnValueTest: {
  index: 0,
  comparator: '>',
  value: 0,
},
```

`returnValueTest` is going to evaluate the contract call result according to the following logic:

* Take the first value returned by the contract call, `index: 0`
* Compare it using the following comparator, `comparator: '>'`
* Compare it to the following value, `value: 0`

Combining these three, we can see that the `returnValueTest` will "trigger" if the value returned by the contract call is greater than zero.

In other words, our condition is only satisfied if `ERC721.balanceOf(:userAddress)[0] > 0`

### Learn more

* [references.md](../references.md "mention")
* [advanced-usage](../advanced-usage/ "mention")
