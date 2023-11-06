# Implement access revocation via smart contract

It is possible to implement _Revocation_ using Conditions that rely on a function call to a Custom Smart Contract. This allows the handling of revocation to be decentralized and transparent. Here is an example of a smart contract (not suitable for production):

```solidity
pragma solidity 0.8.7;

contract Revocation {

    mapping(address => bool) public isRevoked;

    function revoke(address user) public {
        isRevoked[user] = true;
    }

    function unRevoke(address user) public {
        isRevoked[user] = false;
    }
}
```

And the associated Condition:

```javascript
const revocationCondition = {
  contractAddress: '<DEPLOYED_CONTRACT_ADDRESS>',
  method: 'isRevoked',
  parameters: [':userAddress'],
  functionAbi: {
    inputs: [
      {
        internalType: 'address',
        name: '',
        type: 'address',
      },
    ],
    name: 'isRevoked',
    outputs: [
      {
        internalType: 'bool',
        name: '',
        type: 'bool',
      },
    ],
    stateMutability: 'view',
    type: 'function',
  },
  chain: 1,
  returnValueTest: {
    comparator: '==',
    value: false,
  },
};
```

The condition we have defined calls the `isRevoked` function of the smart contract and passes the user's address. If the call returns `false` (**not** revoked, i.e. granted), then decryption will occur. If the call returns `true` (**is** revoked), then decryption will fail.
