# CompoundCondition

`CompoundConditons` allow us to compose conditions into more complex logical statements.

Let's take a look at this abbreviated condition example:

```typescript
import { conditions } from '@nucypher/taco';

const conditionA = ...
const conditionB = ...

const aAndB = new conditions.CompoundCondition({
  operator: 'and',
  operands: [conditionA, conditionB],
});
```

Here, we define two conditions `conditionA` and `conditionB`, and combine them into a single condition using `and` operator. So now, our new condition will "trigger" only when both `conditionA` _and_ `conditionB` "trigger".

We can use two or more  conditions when using `and` and `or` operators:

```typescript
const allMustPass = new conditions.base.CompoundCondition({
  operator: 'and',
  operands: [conditionA, conditionB, ..., conditionX],
});
```

Alternatively, use can use `CompoundCondition.or` and `CompoundCondition.and` short-hand methods

```typescript
const allMustMatch = new conditions.CompoundCondition.and([
    conditionA, conditionB, ..., conditionX],
]);

const atLeastOneMustMatch = new conditions.CompoundCondition.or([
    conditionA, conditionB, ..., conditionX],
]);
```

### Learn more&#x20;

* [references.md](../references.md "mention")
* [advanced-usage](../advanced-usage/ "mention")
