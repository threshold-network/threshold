# TimeCondition

`TimeCondition` allow us to base access to our data on the evaluation of the block height of a target chain.

Below, we can see an example of using  `TimeCondition`&#x20;

```typescript
import { conditions } from '@nucypher/taco';

const ownsNFT = new conditions.base.TimeCondition({
  method: 'blocktime',
  chain: 5,
  returnValueTest: {
    index: 0,
    comparator: '>=',
    value: 1000000,
  },
});

```

`TimeCondition` contains `returnValueTest` which we can use select a comparison operator `comparator` and the desired block height threshold, `1000000`.

### Learn more&#x20;

* [references.md](../references.md "mention")
* [advanced-usage](../advanced-usage/ "mention")

