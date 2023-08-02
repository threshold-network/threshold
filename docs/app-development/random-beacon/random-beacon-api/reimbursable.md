# Solidity API

## Reimbursable

{% hint style="warning" %}
This file documents a contract which is not yet deployed to Mainnet.
{% endhint %}

### reimbursementPool

```solidity
contract ReimbursementPool reimbursementPool
```

### ReimbursementPoolUpdated

```solidity
event ReimbursementPoolUpdated(address newReimbursementPool)
```

### refundable

```solidity
modifier refundable(address receiver)
```

### onlyReimbursableAdmin

```solidity
modifier onlyReimbursableAdmin()
```

### updateReimbursementPool

```solidity
function updateReimbursementPool(contract ReimbursementPool _reimbursementPool) external
```

