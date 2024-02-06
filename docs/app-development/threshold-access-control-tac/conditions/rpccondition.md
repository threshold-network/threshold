# RpcCondition

`RpcConditions` allows data access based on the evaluation of a JSON-RPC call.

Below, we can see an example of using  `RpcCondition` to gate access based on a native asset balance.&#x20;

```typescript
import { conditions } from '@nucypher/taco';

const ownsETH = new conditions.base.rpc.RpcCondition({
  method: 'eth_getBalance',
  parameters: [':userAddress'],
  chain: 1,
  returnValueTest: {
    comparator: '>=',
    value: 1,
  },
});

```

Knowing the [signature of this method](https://ethereum.org/en/developers/docs/apis/json-rpc/#eth\_getbalance), we need to pass a wallet address parameter into the `parameters` field (the default block used is `latest`).

```typescript
 method: 'eth_getBalance',
 parameters: [':userAddress'],
```

`':userAddress'` is a reserved [context variable](https://app.gitbook.com/o/R2meumXNNad4y1B10iL7/s/WosjlL4zUGUMlcMfuSAp/\~/changes/274/app-development/threshold-access-control-tac/conditions/context-and-context-parameters) that denotes the address of the recipient that will attempt to decrypt our data. Before the attempt is made, the `':userAddress'` value will be replaced with the actual wallet address.

Now that we've specified our contract call, we need to figure out what to do with the contract call results. Let's take a look at the `returnValueTest` field:

```typescript
returnValueTest: {
  comparator: '>=',
  value: 1,
},
```

`returnValueTest` is going to evaluate the contract call result according to the following logic:

* Since the RPC call returns only one value, we don't need to specify the `index` field
* Compare it using the following comparator, `comparator: '>='`
* Compare the value returned by the RPC call to the following value, `value: 1`

Combining these three, we can see that the `returnValueTest` will "trigger" if the value returned by the RPC call is greater than one.

In other words, our condition is only satisfied if `eth_getBalance(:userAddress) >= 1` i.e. only if the user's wallet has a native balance greater than or equal to 1.

### Learn more&#x20;

* [references.md](../references.md "mention")
* [advanced-usage](../advanced-usage/ "mention")

