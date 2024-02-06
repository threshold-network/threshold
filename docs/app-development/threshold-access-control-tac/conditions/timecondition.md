# TimeCondition

`TimeCondition` allows data access based on the evaluation of the timestamp (Unix epoch) of the latest block for a target chain.

Here is an example of using  `TimeCondition` .

```typescript
import { conditions } from '@nucypher/taco';

const ownsNFT = new conditions.base.time.TimeCondition({
  chain: 1,
  returnValueTest: {
    comparator: '>=',
    value: 1701428400,
  },
});

```

`TimeCondition` contains `returnValueTest` which we can use to select a comparison operator `comparator` and the desired block timestamp threshold of `1701428400` (`2023-12-01T11:00:00Z`). In this case, access won't be granted until the latest block timestamp is greater than or equal to `1701428400`.

### Learn more&#x20;

* [references.md](../references.md "mention")
* [advanced-usage](../advanced-usage/ "mention")

