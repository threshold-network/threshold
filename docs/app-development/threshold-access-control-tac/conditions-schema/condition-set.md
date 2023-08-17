# Condition Set

Conditions can be combined into Condition Sets using `AND` and `OR` operators.

The below example shows how to authenticate that a requester owns an NFT in one of two different collections.

```javascript
const genuineUndead = new Conditions.ERC721Balance({
  contractAddress: '0x209e639a0EC166Ac7a1A4bA41968fa967dB30221',
});
const gnomePals = new Conditions.ERC721Balance({
  contractAddress: '0x5dB11d7356aa4C0E85Aa5b255eC2B5F81De6d4dA',
});
const NFTConditionSet = new ConditionSet([
  genuineUndead,
  Operator.OR(),
  gnomePals,
]);
```

If we wanted to store this Condition Set for later use we could export it to JSON:

```javascript
const NFTConditionSetJSON = NFTConditionSet.toJSON();
console.log(NFTConditionSetJSON);
// [
//     {
//         "chain": "ethereum",
//         "method": "balanceOf",
//         "parameters": [
//             ":userAddress"
//         ],
//         "standardContractType": "ERC721",
//         "returnValueTest": {
//             "comparator": ">",
//             "value": 0
//         },
//         "contractAddress": "0x209e639a0EC166Ac7a1A4bA41968fa967dB30221"
//     },
//     {
//         "operator": "or"
//     },
//     {
//         "chain": "ethereum",
//         "method": "balanceOf",
//         "parameters": [
//             ":userAddress"
//         ],
//         "standardContractType": "ERC721",
//         "returnValueTest": {
//             "comparator": ">",
//             "value": 0
//         },
//         "contractAddress": "0x5dB11d7356aa4C0E85Aa5b255eC2B5F81De6d4dA"
//     }
// ]
```

And then easily import:

```javascript
const newConditionSet = ConditionSet.fromJSON(NFTConditionSetJSON);
```
