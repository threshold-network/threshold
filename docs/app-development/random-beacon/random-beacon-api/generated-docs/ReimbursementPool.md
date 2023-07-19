# Solidity API

## ReimbursementPool

{% hint style="warning" %}
This file documents a contract which is not yet deployed to Mainnet.
{% endhint %}

### isAuthorized

```solidity
mapping(address => bool) isAuthorized
```

Authorized contracts that can interact with the reimbursment pool.
Authorization can be granted and removed by the owner.

### staticGas

```solidity
uint256 staticGas
```

Static gas includes:
- cost of the refund function
- base transaction cost

### maxGasPrice

```solidity
uint256 maxGasPrice
```

Max gas price used to reimburse a transaction submitter. Protects
against malicious operator-miners.

### StaticGasUpdated

```solidity
event StaticGasUpdated(uint256 newStaticGas)
```

### MaxGasPriceUpdated

```solidity
event MaxGasPriceUpdated(uint256 newMaxGasPrice)
```

### SendingEtherFailed

```solidity
event SendingEtherFailed(uint256 refundAmount, address receiver)
```

### AuthorizedContract

```solidity
event AuthorizedContract(address thirdPartyContract)
```

### UnauthorizedContract

```solidity
event UnauthorizedContract(address thirdPartyContract)
```

### FundsWithdrawn

```solidity
event FundsWithdrawn(uint256 withdrawnAmount, address receiver)
```

### constructor

```solidity
constructor(uint256 _staticGas, uint256 _maxGasPrice) public
```

### receive

```solidity
receive() external payable
```

Receive ETH

### refund

```solidity
function refund(uint256 gasSpent, address receiver) external
```

Refunds ETH to a spender for executing specific transactions.

Ignoring the result of sending ETH to a receiver is made on purpose.
For EOA receiving ETH should always work. If a receiver is a smart
contract, then we do not want to fail a transaction, because in some
cases the refund is done at the very end of multiple calls where all
the previous calls were already paid off. It is a receiver's smart
contract resposibility to make sure it can receive ETH.
Only authorized contracts are allowed calling this function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| gasSpent | uint256 | Gas spent on a transaction that needs to be reimbursed. |
| receiver | address | Address where the reimbursment is sent. |

### authorize

```solidity
function authorize(address _contract) external
```

Authorize a contract that can interact with this reimbursment pool.
Can be authorized by the owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _contract | address | Authorized contract. |

### unauthorize

```solidity
function unauthorize(address _contract) external
```

Unauthorize a contract that was previously authorized to interact
with this reimbursment pool. Can be unauthorized by the
owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _contract | address | Authorized contract. |

### setStaticGas

```solidity
function setStaticGas(uint256 _staticGas) external
```

Setting a static gas cost for executing a transaction. Can be set
by the owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _staticGas | uint256 | Static gas cost. |

### setMaxGasPrice

```solidity
function setMaxGasPrice(uint256 _maxGasPrice) external
```

Setting a max gas price for transactions. Can be set by the
owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _maxGasPrice | uint256 | Max gas price used to reimburse tx submitters. |

### withdrawAll

```solidity
function withdrawAll(address receiver) external
```

Withdraws all ETH from this pool which are sent to a given
address. Can be set by the owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| receiver | address | An address where ETH is sent. |

### withdraw

```solidity
function withdraw(uint256 amount, address receiver) public
```

Withdraws ETH amount from this pool which are sent to a given
address. Can be set by the owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | Amount to withdraw from the pool. |
| receiver | address | An address where ETH is sent. |

