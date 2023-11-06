# Gatekeeping access with an RPC condition

Using `RpcCondition`, we can execute one of the two available JSON-RPC methods: `eth_getBalance` (in the example below) and `balanceOf`.

```typescript
import { conditions } from '@nucypher/taco';

const hasAnyNativeAsset = new conditions.base.RpcCondition({
  chain: 5,
  method: 'eth_getBalance',
  parameters: [':userAddress'],
  returnValueTest: {
    index: 0,
    comparator: '>',
    value: 0,
  },
});
```

### Learn more&#x20;

* [references.md](../../references.md "mention")
* [advanced-usage](../../advanced-usage/ "mention")
