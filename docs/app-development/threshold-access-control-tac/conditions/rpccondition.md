# RpcCondition

`RpcConditions` allow us to base access to our data on the evaluation of a JSON-RPC call to the network node.

Below, we can see an example of using  `RpcCondition` to gate access to our data behind a native asset balance.&#x20;

```typescript
import { conditions } from '@nucypher/taco';

const ownsNFT = new conditions.base.RpcCondition({
  method: 'eth_getBalance',
  parameters: [':userAddress'],
  chain: 5,
  returnValueTest: {
    index: 0,
    comparator: '>=',
    value: 1,
  },
});

```

Knowing the signature of this method, `balanceOf(address),`we need to pass one parameter into the `parameters` field.

```typescript
 method: 'eth_getBalance',
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
  comparator: '>=',
  value: 1,
},
```

`returnValueTest` is going to evaluate the contract call result according to the following logic:

* Take the first value returned by the contract call, `index: 0`
* Compare it using the following comparator, `comparator: '>='`
* Compare it to the following value, `value: 1`

Combining these three, we can see that the `returnValueTest` will "trigger" if the value returned by the RPC call is greater than one.

In other words, our condition is only satisfied if `ERC721.`eth\_getBalance`(:userAddress)[0] >= 1`

### Learn more&#x20;

* [references.md](../references.md "mention")
* [advanced-usage](../advanced-usage/ "mention")

