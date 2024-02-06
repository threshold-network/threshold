# Gatekeeping access with an RPC condition

Using `RpcCondition`, we can execute  `eth_getBalance` JSON-RPC method:

```typescript
import { conditions } from '@nucypher/taco';

const hasAnyNativeAsset = new conditions.base.rpc.RpcCondition({
  chain: 1,
  method: 'eth_getBalance',
  parameters: [':userAddress'],
  returnValueTest: {
    comparator: '>',
    value: 0,
  },
});
```

### Learn more&#x20;

* [references.md](../../references.md "mention")
* [advanced-usage](../../advanced-usage/ "mention")
