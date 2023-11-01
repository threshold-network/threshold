# Solidity API

## GovernanceUtils

### onlyAfterGovernanceDelay

```solidity
function onlyAfterGovernanceDelay(uint256 changeInitiatedTimestamp, uint256 delay) internal view
```

Reverts if the governance delay has not passed since
the change initiated time or if the change has not been
initiated.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| changeInitiatedTimestamp | uint256 | The timestamp at which the change has been initiated. |
| delay | uint256 | Governance delay. |

### getRemainingGovernanceDelay

```solidity
function getRemainingGovernanceDelay(uint256 changeInitiatedTimestamp, uint256 delay) internal view returns (uint256)
```

Gets the time remaining until the governable parameter update
can be committed.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| changeInitiatedTimestamp | uint256 | Timestamp indicating the beginning of the change. |
| delay | uint256 | Governance delay. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

