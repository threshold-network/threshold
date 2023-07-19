# Solidity API

## Governable

{% hint style="warning" %}
This file documents a contract which is not yet deployed to Mainnet.
{% endhint %}

Governable contract.

A constructor is not defined, which makes the contract compatible with
upgradable proxies. This requires calling explicitly `_transferGovernance`
function in a child contract.

### governance

```solidity
address governance
```

### GovernanceTransferred

```solidity
event GovernanceTransferred(address oldGovernance, address newGovernance)
```

### onlyGovernance

```solidity
modifier onlyGovernance()
```

### transferGovernance

```solidity
function transferGovernance(address newGovernance) external virtual
```

Transfers governance of the contract to `newGovernance`.

### _transferGovernance

```solidity
function _transferGovernance(address newGovernance) internal virtual
```

