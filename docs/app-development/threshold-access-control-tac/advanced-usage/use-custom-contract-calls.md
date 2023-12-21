# Use custom contract calls

In this section we show how to use implement support for custom contract calls in `ContractCondition`.

Let's start with the following example:

```typescript
import { conditions } from '@nucypher/taco';

const myFunctionAbi: conditions.base.FunctionAbiProps =  {
  name: 'myFunction',
  type: 'function',
  stateMutability: 'view',
  inputs: [
    {
      internalType: 'address',
      name: 'account',
      type: 'address',
    },
    {
      internalType: 'uint256',
      name: 'myCustomParam',
      type: 'uint256',
    },
  ],
  outputs: [
    {
      internalType: 'uint256',
      name: 'someValue',
      type: 'uint256',
    },
  ],
};

const myContractCallCondition = new conditions.base.ContractCondition({
  method: 'myFunction', // `myMethodAbi.name`
  parameters: [':userAddress', ':myCustomParam'], // `myMethodAbi.inputs`
  functionAbi: myFunctionAbi, // Our custom function ABI
  contractAddress: '0x0...1',
  chain: 5,
  returnValueTest: {
    comparator: '>',
    value: 0,
  },
});
```

First, let's take a look at `myFunctionAbi`:

* We define a complete function ABI with `name`, `type`, `stateMutability`, `inputs`, and `outputs`
* With those fields in place, our function shapes up to be defined as `myFunction(address, uint256): uint256`
* Note that `type` field  required to be `function` and `stateMutability` to `pure` and `view` in order to avoid accidentally mutating the contract state

&#x20;Now, looking at `myContractCallCondition` we can see that:

* We need to pass `myFunction` as `method` and `myFunctionAbi` as `functionAbi` in order for our contract call to be recognized correctly
* We've mapped our function parameters to `parameters`, so that `address` is represented as `':userAddress'` and `uint256` is represented as `':myCustomParam'` [context parameters](https://app.gitbook.com/o/R2meumXNNad4y1B10iL7/s/WosjlL4zUGUMlcMfuSAp/\~/changes/274/app-development/threshold-access-control-tac/conditions/context-and-context-parameters)
* Lastly, the `myFunctionAbi.outputs` will be used by `returnValueTest` to compare with the selected threshold `value`, resulting in a test `myFunctionAbi.outputs[0] > 0`

### Learn more

* See how to implement access control revocation using custom contract calls [here](implementing-revocation-via-smart-contract.md).