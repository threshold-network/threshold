# CompoundCondition

`CompoundConditon` allow conditions to be combined into more complex logical statements. The logical operators allowed are `or`, `and`, and `not`.

## `or`/`and` Operator

`or` & `and` operators require  >= 2 operands to be specified.

Let's take a look at this abbreviated condition example:

```typescript
import { conditions } from '@nucypher/taco';

const conditionA = ...
const conditionB = ...

const aAndB = new conditions.base.CompoundCondition({
  operator: 'and',
  operands: [conditionA, conditionB],
});
```

Here, we define two conditions `conditionA` and `conditionB`, and combine them into a single condition using `and` operator. So now, our new condition passes only when both `conditionA` _and_ `conditionB` hold true.

We can use two or more conditions when using `and` and `or` operators e.g.:

```typescript
const allMustPass = new conditions.base.CompoundCondition({
  operator: 'and',
  operands: [conditionA, conditionB, ..., conditionX],
});
```

```typescript
const anyOneMustPass = new conditions.base.CompoundCondition({
  operator: 'or',
  operands: [conditionA, conditionB, ..., conditionX],
});
```

Alternatively, use can use `CompoundCondition.or` and `CompoundCondition.and` short-hand methods

```typescript
const allMustPass = new conditions.base.CompoundCondition.and([
    conditionA, conditionB, ..., conditionX],
]);

const anyOneMustPass = new conditions.base.CompoundCondition.or([
    conditionA, conditionB, ..., conditionX],
]);
```

## `not` Operator

The `not` operator only allows one operand.

For example:

```typescript
import { conditions } from '@nucypher/taco';

const conditionA = ...

const notA = new conditions.base.CompoundCondition({
  operator: 'not',
  operands: [conditionA],
});
```

## Nested Combinations

`CompoundCondition`  objects can be nested on itself i.e. you can create compound conditions of compound conditions.

For example:

```typescript
import { conditions } from '@nucypher/taco';

const conditionA = ...
const conditionB = ...
const conditionC = ...
const conditionD = ...
const conditionE = ...

const cOrD = new conditions.base.CompoundCondition({
  operator: 'or',
  operands: [conditionC, conditionD],
});

const notE = new conditions.base.CompoundCondition({
  operator: 'not',
  operands: [conditionE],
});

// conditionA AND conditionB AND cOrD AND notE
overallCondition = new conditions.base.CompoundCondition({
  operator: 'and',
  operands: [conditionA, conditionB, cOrD, notE],
});

```

### Learn more&#x20;

* [references.md](../references.md "mention")
* [advanced-usage](../advanced-usage/ "mention")
