# Solidity API

## Callback

{% hint style="warning" %}
This file documents a contract which is not yet deployed to Mainnet.
{% endhint %}

Library for handling calls to random beacon consumer.

### Data

```solidity
struct Data {
  contract IRandomBeaconConsumer callbackContract;
}
```

### CallbackFailed

```solidity
event CallbackFailed(uint256 entry, uint256 entrySubmittedBlock)
```

### setCallbackContract

```solidity
function setCallbackContract(struct Callback.Data self, contract IRandomBeaconConsumer callbackContract) internal
```

Sets callback contract.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Callback.Data |  |
| callbackContract | contract IRandomBeaconConsumer | Callback contract. |

### executeCallback

```solidity
function executeCallback(struct Callback.Data self, uint256 entry, uint256 callbackGasLimit) internal
```

Executes consumer specified callback for the relay entry request.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Callback.Data |  |
| entry | uint256 | The generated random number. |
| callbackGasLimit | uint256 | Callback gas limit. |

