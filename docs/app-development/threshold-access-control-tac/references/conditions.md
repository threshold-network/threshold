# Conditions

This page focuses on Condition types and composition. To understand how Conditions are added and enforced with respect to runtime, check out the [Condition Hierarchies](../advanced-usage/condition-hierarchies.md) page. \
\
Several distinct categories of access conditions can be specified and combined:&#x20;

* EVM - on-chain state, eg NFT ownership, ETH balance, tx status, contract function call
* RPC - ethereum RPC calls as defined in the [Official API](https://ethereum.org/en/developers/docs/apis/json-rpc/#json-rpc-methods)
* Timelock - time-based conditions, eg Block Height

We provide many helper objects to streamline the creation of common conditions. An expressive API also allows much more granular control of conditions, and we will provide examples of both methods wherever possible.

## `Conditions.ERC721Ownership`

`Conditions.ERC721Ownership` is a shortcut for building conditions that test for ownership of a specific ERC721 token (NFT):

```javascript
const NFTOwnership = new Conditions.ERC721Ownership({
  contractAddress: '0xBC4CA0EdA7647A8aB7C2061c2E118A18a936f13D',
  parameters: [5954],
});
```

If we want to be more verbose we can use `Conditions.Condition`. The above and below examples are completely equivalent:

```javascript
const NFTOwnershipConfig = {
  contractAddress: '0xBC4CA0EdA7647A8aB7C2061c2E118A18a936f13D',
  standardContractType: 'ERC721',
  chain: 1,
  method: 'ownerOf',
  parameters: [5954],
  returnValueTest: {
    comparator: '==',
    value: ':userAddress',
  },
};
const NFTOwnership = new Conditions.Condition(NFTOwnershipConfig);
```

## `Conditions.ERC721Balance`

`Conditions.ERC721Balance` is a shortcut for building conditions that test for ownership of at least one ERC721 token (NFT) within a collection.

```javascript
const NFTBalance = new Conditions.ERC721Balance({
  contractAddress: '0xBC4CA0EdA7647A8aB7C2061c2E118A18a936f13D',
});
```

Alternatively:

```javascript
const NFTBalanceConfig = {
  contractAddress: '0xBC4CA0EdA7647A8aB7C2061c2E118A18a936f13D',
  standardContractType: 'ERC721',
  chain: 5,
  method: 'balanceOf',
  parameters: [':userAddress'],
  returnValueTest: {
    comparator: '>',
    value: 0,
  },
};

const NFTBalance = new Conditions.Condition(NFTBalanceConfig);
```

## `Conditions.TimelockCondition`

`Conditions.TimelockCondition` is a shortcut for building conditions that test against block height.

```javascript
const timelock = Conditions.TimelockCondition({
  returnValueTest: {
    comparator: '>',
    value: 100,
  },
});
```

or:

```javascript
const timelockConfig = {
  contractAddress: '',
  standardContractType: '',
  chain: 5,
  method: 'timelock',
  returnValueTest: {
    comparator: '>',
    value: 100,
  },
};
const timelock = Conditions.Condition(timelockConfig);
```

## `Conditions.RpcCondition`

`Conditions.RpcCondition` is a shortcut for building conditions that test against standard [RPC calls](https://ethereum.org/en/developers/docs/apis/json-rpc/).

```javascript
const rpc = new Conditions.RpcCondition({
  chain: 1,
  method: 'eth_getBalance',
  parameters: [
      ":userAddress",
      "latest"
    ],
  returnValueTest: {
    comparator: ">=",
    value: 10000000000000
  }
});
```

or:

```javascript
const rpcConfig = {
  contractAddress: '',
  standardContractType: '',
  chain: 1,
  method: 'eth_getBalance',
  parameters: [':userAddress', 'latest'],
  returnValueTest: {
    comparator: '>=',
    value: 10000000000000,
  },
};
const rpc = Conditions.Condition(rpcConfig);
```

## `Conditions.Condition`

`Conditions.Condition` provides full control over the configuration of a Condition. It takes parameters:

* `contractAddress` is the public address of the contract we'd like to query.
* `standardContractType` can take values from `ERC20` and `ERC721`. Alternatively, an ABI can be passed through if a non standard contract is being used.
* `functionAbi` is the ABI of the function we'd like to call. This is optional if the contract is a standard `ERC20`, `ERC721` or `ERC1155`.
* `chain` - currently only `ethereum` is supported, please [Contact Us](https://discord.gg/RwjHbgA7uQ) if you require non-Ethereum based conditions.
* `method` the contract method that will be called.
* `parameters` are the parameters that will be passed to the contract's `method`.
* `returnValueTest` defines how the return value of the contract call should be evaluated.

### Non Zero balance of ERC20 Token

Here we're checking whether the user owns any `T` Threshold Network token:

```javascript
const ERC20Conditions = {
  contractAddress: '0xCdF7028ceAB81fA0C6971208e83fa7872994beE5',
  standardContractType: 'ERC20',
  chain: 5,
  method: 'balanceOf',
  parameters: [':userAddress'],
  returnValueTest: {
    comparator: '>',
    value: 0,
  },
};
```

### Function call of nonstandard Contract

In this example, we will check that the user is able to vote in the `T` Threshold DAO. The Threshold staking contract is located at [`0x01B67b1194C75264d06F808A921228a95C765dd7`](https://etherscan.io/address/0x01b67b1194c75264d06f808a921228a95c765dd7#readProxyContract). The function we wish to call is `getVotes` which takes an `address` as its parameter. We need to provide the `contractAddress`, `functionName`, `functionParams`, and `functionAbi` when defining the condition.

```javascript
const customABICondition = {
  contractAddress: '0x01B67b1194C75264d06F808A921228a95C765dd7',
  method: 'getVotes',
  parameters: [':userAddress'],
  functionAbi: {
    inputs: [
      {
        internalType: 'address',
        name: 'account',
        type: 'address',
      },
    ],
    name: 'getVotes',
    outputs: [
      {
        internalType: 'uint96',
        name: '',
        type: 'uint96',
      },
    ],
    stateMutability: 'view',
    type: 'function',
  },
  chain: 1,
  returnValueTest: {
    comparator: '>',
    value: 0,
  },
};
```
