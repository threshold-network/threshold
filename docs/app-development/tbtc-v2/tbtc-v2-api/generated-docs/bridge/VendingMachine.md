# Solidity API

## VendingMachine

The Vending Machine is the owner of TBTC v2 token and can mint
TBTC v2 tokens in 1:1 ratio from TBTC v1 tokens with TBTC v1
deposited in the contract as collateral. TBTC v2 can be
unminted back to TBTC v1 with or without a fee - fee parameter is
controlled by the Governance. This implementation acts as a bridge
between TBTC v1 and TBTC v2 token, allowing to mint TBTC v2 before
the system is ready and fully operational without sacrificing any
security guarantees and decentralization of the project.
Vending Machine can be upgraded in a two-step, governance-controlled
process. The new version of the Vending Machine will receive the
ownership of TBTC v2 token and entire TBTC v1 balance stored as
collateral. It is expected that this process will be executed before
the v2 system launch. There is an optional unmint fee with a value
that can be updated in a two-step, governance-controlled process.
All governable parameters are controlled by two roles: update
initiator and finalizer. There is a separate initiator role for
unmint fee update and vending machine upgrade. The initiator
proposes the change by initiating the update and the finalizer
(contract owner) may approve it by finalizing the change after the
governance delay passes.

### GOVERNANCE_DELAY

```solidity
uint256 GOVERNANCE_DELAY
```

The time delay that needs to pass between initializing and
finalizing update of any governable parameter in this contract.

### FLOATING_POINT_DIVISOR

```solidity
uint256 FLOATING_POINT_DIVISOR
```

Divisor for precision purposes. Used to represent fractions
in parameter values.

### tbtcV1

```solidity
contract IERC20 tbtcV1
```

### tbtcV2

```solidity
contract TBTC tbtcV2
```

### unmintFee

```solidity
uint256 unmintFee
```

The fee for unminting TBTC v2 back into TBTC v1 represented as
1e18 precision fraction. The fee is proportional to the amount
being unminted and added on the top of the amount being unminted.
To calculate the fee value, the amount being unminted needs
to be multiplied by `unmintFee` and divided by 1e18.
For example, `unmintFee` set to 1000000000000000
means that 0.001 of the amount being unminted needs to be paid
to the `VendingMachine` as an unminting fee on the top of the
amount being unminted.

### newUnmintFee

```solidity
uint256 newUnmintFee
```

### unmintFeeUpdateInitiatedTimestamp

```solidity
uint256 unmintFeeUpdateInitiatedTimestamp
```

### unmintFeeUpdateInitiator

```solidity
address unmintFeeUpdateInitiator
```

### newVendingMachine

```solidity
address newVendingMachine
```

The address of a new vending machine. Set only when the upgrade
process is pending. Once the upgrade gets finalized, the new
vending machine will become an owner of TBTC v2 token.

### vendingMachineUpgradeInitiatedTimestamp

```solidity
uint256 vendingMachineUpgradeInitiatedTimestamp
```

### vendingMachineUpgradeInitiator

```solidity
address vendingMachineUpgradeInitiator
```

### UnmintFeeUpdateInitiated

```solidity
event UnmintFeeUpdateInitiated(uint256 newUnmintFee, uint256 timestamp)
```

### UnmintFeeUpdated

```solidity
event UnmintFeeUpdated(uint256 newUnmintFee)
```

### VendingMachineUpgradeInitiated

```solidity
event VendingMachineUpgradeInitiated(address newVendingMachine, uint256 timestamp)
```

### VendingMachineUpgraded

```solidity
event VendingMachineUpgraded(address newVendingMachine)
```

### Minted

```solidity
event Minted(address recipient, uint256 amount)
```

### Unminted

```solidity
event Unminted(address recipient, uint256 amount, uint256 fee)
```

### only

```solidity
modifier only(address authorizedCaller)
```

### onlyAfterGovernanceDelay

```solidity
modifier onlyAfterGovernanceDelay(uint256 changeInitiatedTimestamp)
```

### constructor

```solidity
constructor(contract IERC20 _tbtcV1, contract TBTC _tbtcV2, uint256 _unmintFee) public
```

### mint

```solidity
function mint(uint256 amount) external
```

Mints TBTC v2 to the caller from TBTC v1 with 1:1 ratio.
The caller needs to have at least `amount` of TBTC v1 balance
approved for transfer to the `VendingMachine` before calling
this function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | The amount of TBTC v2 to mint from TBTC v1 |

### receiveApproval

```solidity
function receiveApproval(address from, uint256 amount, address token, bytes) external
```

Mints TBTC v2 to `from` address from TBTC v1 with 1:1 ratio.
`from` address needs to have at least `amount` of TBTC v1
balance approved for transfer to the `VendingMachine` before
calling this function.

This function is a shortcut for approve + mint. Only TBTC v1
caller is allowed and only TBTC v1 is allowed as a token to
transfer.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| from | address | TBTC v1 token holder minting TBTC v2 tokens |
| amount | uint256 | The amount of TBTC v2 to mint from TBTC v1 |
| token | address | TBTC v1 token address |
|  | bytes |  |

### unmint

```solidity
function unmint(uint256 amount) external
```

Unmints TBTC v2 from the caller into TBTC v1. Depending on
`unmintFee` value, may require paying an additional unmint fee
in TBTC v2 in addition to the amount being unminted. To see
what is the value of the fee, please call `unmintFeeFor(amount)`
function. The caller needs to have at least
`amount + unmintFeeFor(amount)` of TBTC v2 balance approved for
transfer to the `VendingMachine` before calling this function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | The amount of TBTC v2 to unmint to TBTC v1 |

### withdrawFees

```solidity
function withdrawFees(address recipient, uint256 amount) external
```

Allows the Governance to withdraw unmint fees accumulated by
`VendingMachine`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| recipient | address | The address receiving the fees |
| amount | uint256 | The amount of fees in TBTC v2 to withdraw |

### initiateUnmintFeeUpdate

```solidity
function initiateUnmintFeeUpdate(uint256 _newUnmintFee) external
```

Initiates unmint fee update process. The update process needs to
be finalized with a call to `finalizeUnmintFeeUpdate` function
after the `GOVERNANCE_DELAY` passes. Only unmint fee update
initiator role can initiate the update.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newUnmintFee | uint256 | The new unmint fee |

### finalizeUnmintFeeUpdate

```solidity
function finalizeUnmintFeeUpdate() external
```

Allows the contract owner to finalize unmint fee update process.
The update process needs to be first initiated with a call to
`initiateUnmintFeeUpdate` and the `GOVERNANCE_DELAY` needs to
pass.

### initiateVendingMachineUpgrade

```solidity
function initiateVendingMachineUpgrade(address _newVendingMachine) external
```

Initiates vending machine upgrade process. The upgrade process
needs to be finalized with a call to
`finalizeVendingMachineUpgrade` function after the
`GOVERNANCE_DELAY` passes. Only vending machine upgrade
initiator role can initiate the upgrade.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newVendingMachine | address | The new vending machine address |

### finalizeVendingMachineUpgrade

```solidity
function finalizeVendingMachineUpgrade() external
```

Allows the contract owner to finalize vending machine upgrade
process. The upgrade process needs to be first initiated with a
call to `initiateVendingMachineUpgrade` and the `GOVERNANCE_DELAY`
needs to pass. Once the upgrade is finalized, the new vending
machine will become an owner of TBTC v2 token and all TBTC v1
held by this contract will be transferred to the new vending
machine.

### transferUnmintFeeUpdateInitiatorRole

```solidity
function transferUnmintFeeUpdateInitiatorRole(address newInitiator) external
```

Transfers unmint fee update initiator role to another address.
Can be called only by the current unmint fee update initiator.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| newInitiator | address | The new unmint fee update initiator |

### transferVendingMachineUpgradeInitiatorRole

```solidity
function transferVendingMachineUpgradeInitiatorRole(address newInitiator) external
```

Transfers vending machine upgrade initiator role to another
address. Can be called only by the current vending machine
upgrade initiator.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| newInitiator | address | The new vending machine upgrade initiator |

### getRemainingUnmintFeeUpdateTime

```solidity
function getRemainingUnmintFeeUpdateTime() external view returns (uint256)
```

Get the remaining time that needs to pass until unmint fee
update can be finalized by the Governance. If the update has
not been initiated, the function reverts.

### getRemainingVendingMachineUpgradeTime

```solidity
function getRemainingVendingMachineUpgradeTime() external view returns (uint256)
```

Get the remaining time that needs to pass until vending machine
upgrade can be finalized by the Governance. If the upgrade has
not been initiated, the function reverts.

### unmintFeeFor

```solidity
function unmintFeeFor(uint256 amount) public view returns (uint256)
```

Calculates the fee that needs to be paid to the `VendingMachine`
to unmint the given amount of TBTC v2 back into TBTC v1.

### _mint

```solidity
function _mint(address tokenOwner, uint256 amount) internal
```

