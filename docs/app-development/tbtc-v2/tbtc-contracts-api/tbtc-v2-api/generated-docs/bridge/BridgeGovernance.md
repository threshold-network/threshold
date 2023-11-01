# Solidity API

## BridgeGovernance

Owns the `Bridge` contract and is responsible for updating
its governable parameters in respect to governance delay individual
for each parameter. The other responsibility is marking a vault
address as trusted or no longer trusted.

### depositData

```solidity
struct BridgeGovernanceParameters.DepositData depositData
```

### redemptionData

```solidity
struct BridgeGovernanceParameters.RedemptionData redemptionData
```

### movingFundsData

```solidity
struct BridgeGovernanceParameters.MovingFundsData movingFundsData
```

### walletData

```solidity
struct BridgeGovernanceParameters.WalletData walletData
```

### fraudData

```solidity
struct BridgeGovernanceParameters.FraudData fraudData
```

### treasuryData

```solidity
struct BridgeGovernanceParameters.TreasuryData treasuryData
```

### bridge

```solidity
contract Bridge bridge
```

### governanceDelays

```solidity
uint256[3] governanceDelays
```

### bridgeGovernanceTransferChangeInitiated

```solidity
uint256 bridgeGovernanceTransferChangeInitiated
```

### newBridgeGovernance

```solidity
address newBridgeGovernance
```

### BridgeGovernanceTransferStarted

```solidity
event BridgeGovernanceTransferStarted(address newBridgeGovernance, uint256 timestamp)
```

### DepositDustThresholdUpdateStarted

```solidity
event DepositDustThresholdUpdateStarted(uint64 newDepositDustThreshold, uint256 timestamp)
```

### DepositDustThresholdUpdated

```solidity
event DepositDustThresholdUpdated(uint64 depositDustThreshold)
```

### DepositTreasuryFeeDivisorUpdateStarted

```solidity
event DepositTreasuryFeeDivisorUpdateStarted(uint64 depositTreasuryFeeDivisor, uint256 timestamp)
```

### DepositTreasuryFeeDivisorUpdated

```solidity
event DepositTreasuryFeeDivisorUpdated(uint64 depositTreasuryFeeDivisor)
```

### DepositTxMaxFeeUpdateStarted

```solidity
event DepositTxMaxFeeUpdateStarted(uint64 newDepositTxMaxFee, uint256 timestamp)
```

### DepositTxMaxFeeUpdated

```solidity
event DepositTxMaxFeeUpdated(uint64 depositTxMaxFee)
```

### DepositRevealAheadPeriodUpdateStarted

```solidity
event DepositRevealAheadPeriodUpdateStarted(uint32 newDepositRevealAheadPeriod, uint256 timestamp)
```

### DepositRevealAheadPeriodUpdated

```solidity
event DepositRevealAheadPeriodUpdated(uint32 depositRevealAheadPeriod)
```

### RedemptionDustThresholdUpdateStarted

```solidity
event RedemptionDustThresholdUpdateStarted(uint64 newRedemptionDustThreshold, uint256 timestamp)
```

### RedemptionDustThresholdUpdated

```solidity
event RedemptionDustThresholdUpdated(uint64 redemptionDustThreshold)
```

### RedemptionTreasuryFeeDivisorUpdateStarted

```solidity
event RedemptionTreasuryFeeDivisorUpdateStarted(uint64 newRedemptionTreasuryFeeDivisor, uint256 timestamp)
```

### RedemptionTreasuryFeeDivisorUpdated

```solidity
event RedemptionTreasuryFeeDivisorUpdated(uint64 redemptionTreasuryFeeDivisor)
```

### RedemptionTxMaxFeeUpdateStarted

```solidity
event RedemptionTxMaxFeeUpdateStarted(uint64 newRedemptionTxMaxFee, uint256 timestamp)
```

### RedemptionTxMaxFeeUpdated

```solidity
event RedemptionTxMaxFeeUpdated(uint64 redemptionTxMaxFee)
```

### RedemptionTxMaxTotalFeeUpdateStarted

```solidity
event RedemptionTxMaxTotalFeeUpdateStarted(uint64 newRedemptionTxMaxTotalFee, uint256 timestamp)
```

### RedemptionTxMaxTotalFeeUpdated

```solidity
event RedemptionTxMaxTotalFeeUpdated(uint64 redemptionTxMaxTotalFee)
```

### RedemptionTimeoutUpdateStarted

```solidity
event RedemptionTimeoutUpdateStarted(uint32 newRedemptionTimeout, uint256 timestamp)
```

### RedemptionTimeoutUpdated

```solidity
event RedemptionTimeoutUpdated(uint32 redemptionTimeout)
```

### RedemptionTimeoutSlashingAmountUpdateStarted

```solidity
event RedemptionTimeoutSlashingAmountUpdateStarted(uint96 newRedemptionTimeoutSlashingAmount, uint256 timestamp)
```

### RedemptionTimeoutSlashingAmountUpdated

```solidity
event RedemptionTimeoutSlashingAmountUpdated(uint96 redemptionTimeoutSlashingAmount)
```

### RedemptionTimeoutNotifierRewardMultiplierUpdateStarted

```solidity
event RedemptionTimeoutNotifierRewardMultiplierUpdateStarted(uint32 newRedemptionTimeoutNotifierRewardMultiplier, uint256 timestamp)
```

### RedemptionTimeoutNotifierRewardMultiplierUpdated

```solidity
event RedemptionTimeoutNotifierRewardMultiplierUpdated(uint32 redemptionTimeoutNotifierRewardMultiplier)
```

### MovingFundsTxMaxTotalFeeUpdateStarted

```solidity
event MovingFundsTxMaxTotalFeeUpdateStarted(uint64 newMovingFundsTxMaxTotalFee, uint256 timestamp)
```

### MovingFundsTxMaxTotalFeeUpdated

```solidity
event MovingFundsTxMaxTotalFeeUpdated(uint64 movingFundsTxMaxTotalFee)
```

### MovingFundsDustThresholdUpdateStarted

```solidity
event MovingFundsDustThresholdUpdateStarted(uint64 newMovingFundsDustThreshold, uint256 timestamp)
```

### MovingFundsDustThresholdUpdated

```solidity
event MovingFundsDustThresholdUpdated(uint64 movingFundsDustThreshold)
```

### MovingFundsTimeoutResetDelayUpdateStarted

```solidity
event MovingFundsTimeoutResetDelayUpdateStarted(uint32 newMovingFundsTimeoutResetDelay, uint256 timestamp)
```

### MovingFundsTimeoutResetDelayUpdated

```solidity
event MovingFundsTimeoutResetDelayUpdated(uint32 movingFundsTimeoutResetDelay)
```

### MovingFundsTimeoutUpdateStarted

```solidity
event MovingFundsTimeoutUpdateStarted(uint32 newMovingFundsTimeout, uint256 timestamp)
```

### MovingFundsTimeoutUpdated

```solidity
event MovingFundsTimeoutUpdated(uint32 movingFundsTimeout)
```

### MovingFundsTimeoutSlashingAmountUpdateStarted

```solidity
event MovingFundsTimeoutSlashingAmountUpdateStarted(uint96 newMovingFundsTimeoutSlashingAmount, uint256 timestamp)
```

### MovingFundsTimeoutSlashingAmountUpdated

```solidity
event MovingFundsTimeoutSlashingAmountUpdated(uint96 movingFundsTimeoutSlashingAmount)
```

### MovingFundsTimeoutNotifierRewardMultiplierUpdateStarted

```solidity
event MovingFundsTimeoutNotifierRewardMultiplierUpdateStarted(uint32 newMovingFundsTimeoutNotifierRewardMultiplier, uint256 timestamp)
```

### MovingFundsTimeoutNotifierRewardMultiplierUpdated

```solidity
event MovingFundsTimeoutNotifierRewardMultiplierUpdated(uint32 movingFundsTimeoutNotifierRewardMultiplier)
```

### MovingFundsCommitmentGasOffsetUpdateStarted

```solidity
event MovingFundsCommitmentGasOffsetUpdateStarted(uint16 newMovingFundsCommitmentGasOffset, uint256 timestamp)
```

### MovingFundsCommitmentGasOffsetUpdated

```solidity
event MovingFundsCommitmentGasOffsetUpdated(uint16 movingFundsCommitmentGasOffset)
```

### MovedFundsSweepTxMaxTotalFeeUpdateStarted

```solidity
event MovedFundsSweepTxMaxTotalFeeUpdateStarted(uint64 newMovedFundsSweepTxMaxTotalFee, uint256 timestamp)
```

### MovedFundsSweepTxMaxTotalFeeUpdated

```solidity
event MovedFundsSweepTxMaxTotalFeeUpdated(uint64 movedFundsSweepTxMaxTotalFee)
```

### MovedFundsSweepTimeoutUpdateStarted

```solidity
event MovedFundsSweepTimeoutUpdateStarted(uint32 newMovedFundsSweepTimeout, uint256 timestamp)
```

### MovedFundsSweepTimeoutUpdated

```solidity
event MovedFundsSweepTimeoutUpdated(uint32 movedFundsSweepTimeout)
```

### MovedFundsSweepTimeoutSlashingAmountUpdateStarted

```solidity
event MovedFundsSweepTimeoutSlashingAmountUpdateStarted(uint96 newMovedFundsSweepTimeoutSlashingAmount, uint256 timestamp)
```

### MovedFundsSweepTimeoutSlashingAmountUpdated

```solidity
event MovedFundsSweepTimeoutSlashingAmountUpdated(uint96 movedFundsSweepTimeoutSlashingAmount)
```

### MovedFundsSweepTimeoutNotifierRewardMultiplierUpdateStarted

```solidity
event MovedFundsSweepTimeoutNotifierRewardMultiplierUpdateStarted(uint32 newMovedFundsSweepTimeoutNotifierRewardMultiplier, uint256 timestamp)
```

### MovedFundsSweepTimeoutNotifierRewardMultiplierUpdated

```solidity
event MovedFundsSweepTimeoutNotifierRewardMultiplierUpdated(uint32 movedFundsSweepTimeoutNotifierRewardMultiplier)
```

### WalletCreationPeriodUpdateStarted

```solidity
event WalletCreationPeriodUpdateStarted(uint32 newWalletCreationPeriod, uint256 timestamp)
```

### WalletCreationPeriodUpdated

```solidity
event WalletCreationPeriodUpdated(uint32 walletCreationPeriod)
```

### WalletCreationMinBtcBalanceUpdateStarted

```solidity
event WalletCreationMinBtcBalanceUpdateStarted(uint64 newWalletCreationMinBtcBalance, uint256 timestamp)
```

### WalletCreationMinBtcBalanceUpdated

```solidity
event WalletCreationMinBtcBalanceUpdated(uint64 walletCreationMinBtcBalance)
```

### WalletCreationMaxBtcBalanceUpdateStarted

```solidity
event WalletCreationMaxBtcBalanceUpdateStarted(uint64 newWalletCreationMaxBtcBalance, uint256 timestamp)
```

### WalletCreationMaxBtcBalanceUpdated

```solidity
event WalletCreationMaxBtcBalanceUpdated(uint64 walletCreationMaxBtcBalance)
```

### WalletClosureMinBtcBalanceUpdateStarted

```solidity
event WalletClosureMinBtcBalanceUpdateStarted(uint64 newWalletClosureMinBtcBalance, uint256 timestamp)
```

### WalletClosureMinBtcBalanceUpdated

```solidity
event WalletClosureMinBtcBalanceUpdated(uint64 walletClosureMinBtcBalance)
```

### WalletMaxAgeUpdateStarted

```solidity
event WalletMaxAgeUpdateStarted(uint32 newWalletMaxAge, uint256 timestamp)
```

### WalletMaxAgeUpdated

```solidity
event WalletMaxAgeUpdated(uint32 walletMaxAge)
```

### WalletMaxBtcTransferUpdateStarted

```solidity
event WalletMaxBtcTransferUpdateStarted(uint64 newWalletMaxBtcTransfer, uint256 timestamp)
```

### WalletMaxBtcTransferUpdated

```solidity
event WalletMaxBtcTransferUpdated(uint64 walletMaxBtcTransfer)
```

### WalletClosingPeriodUpdateStarted

```solidity
event WalletClosingPeriodUpdateStarted(uint32 newWalletClosingPeriod, uint256 timestamp)
```

### WalletClosingPeriodUpdated

```solidity
event WalletClosingPeriodUpdated(uint32 walletClosingPeriod)
```

### FraudChallengeDepositAmountUpdateStarted

```solidity
event FraudChallengeDepositAmountUpdateStarted(uint96 newFraudChallengeDepositAmount, uint256 timestamp)
```

### FraudChallengeDepositAmountUpdated

```solidity
event FraudChallengeDepositAmountUpdated(uint96 fraudChallengeDepositAmount)
```

### FraudChallengeDefeatTimeoutUpdateStarted

```solidity
event FraudChallengeDefeatTimeoutUpdateStarted(uint32 newFraudChallengeDefeatTimeout, uint256 timestamp)
```

### FraudChallengeDefeatTimeoutUpdated

```solidity
event FraudChallengeDefeatTimeoutUpdated(uint32 fraudChallengeDefeatTimeout)
```

### FraudSlashingAmountUpdateStarted

```solidity
event FraudSlashingAmountUpdateStarted(uint96 newFraudSlashingAmount, uint256 timestamp)
```

### FraudSlashingAmountUpdated

```solidity
event FraudSlashingAmountUpdated(uint96 fraudSlashingAmount)
```

### FraudNotifierRewardMultiplierUpdateStarted

```solidity
event FraudNotifierRewardMultiplierUpdateStarted(uint32 newFraudNotifierRewardMultiplier, uint256 timestamp)
```

### FraudNotifierRewardMultiplierUpdated

```solidity
event FraudNotifierRewardMultiplierUpdated(uint32 fraudNotifierRewardMultiplier)
```

### TreasuryUpdateStarted

```solidity
event TreasuryUpdateStarted(address newTreasury, uint256 timestamp)
```

### TreasuryUpdated

```solidity
event TreasuryUpdated(address treasury)
```

### constructor

```solidity
constructor(contract Bridge _bridge, uint256 _governanceDelay) public
```

### setVaultStatus

```solidity
function setVaultStatus(address vault, bool isTrusted) external
```

Allows the Governance to mark the given vault address as trusted
or no longer trusted. Vaults are not trusted by default.
Trusted vault must meet the following criteria:
- `IVault.receiveBalanceIncrease` must have a known, low gas
cost,
- `IVault.receiveBalanceIncrease` must never revert.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| vault | address | The address of the vault. |
| isTrusted | bool | flag indicating whether the vault is trusted or not. |

### setSpvMaintainerStatus

```solidity
function setSpvMaintainerStatus(address spvMaintainer, bool isTrusted) external
```

Allows the Governance to mark the given address as trusted
or no longer trusted SPV maintainer. Addresses are not trusted
as SPV maintainers by default.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| spvMaintainer | address | The address of the SPV maintainer. |
| isTrusted | bool | flag indicating whether the address is trusted or not. |

### beginGovernanceDelayUpdate

```solidity
function beginGovernanceDelayUpdate(uint256 _newGovernanceDelay) external
```

Begins the governance delay update process.

Can be called only by the contract owner. The event that informs about
the start of the governance delay was skipped on purpose to trim
the contract size. All the params inside of the `governanceDelays`
array are public and can be easily fetched.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newGovernanceDelay | uint256 | New governance delay |

### finalizeGovernanceDelayUpdate

```solidity
function finalizeGovernanceDelayUpdate() external
```

Finalizes the governance delay update process.

Can be called only by the contract owner, after the governance
delay elapses. Updated event was skipped on purpose to trim the
contract size. All the params inside of the `governanceDelays`
array are public and can be easily fetched.

### beginBridgeGovernanceTransfer

```solidity
function beginBridgeGovernanceTransfer(address _newBridgeGovernance) external
```

Begins the Bridge governance transfer process.

Can be called only by the contract owner. It is the governance
responsibility to validate the correctness of the new Bridge
Governance contract. The other reason for not adding this check is
to go down with the contract size and leaving only the essential code.

### finalizeBridgeGovernanceTransfer

```solidity
function finalizeBridgeGovernanceTransfer() external
```

Finalizes the bridge governance transfer process.

Can be called only by the contract owner, after the governance
delay elapses. Bridge governance transferred event can be read
from the Governable bridge contract 'GovernanceTransferred(old, new)'.
Event that informs about the transfer in this function is skipped on
purpose to go down with the contract size.

### beginDepositDustThresholdUpdate

```solidity
function beginDepositDustThresholdUpdate(uint64 _newDepositDustThreshold) external
```

Begins the deposit dust threshold amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDepositDustThreshold | uint64 | New deposit dust threshold amount. |

### finalizeDepositDustThresholdUpdate

```solidity
function finalizeDepositDustThresholdUpdate() external
```

Finalizes the deposit dust threshold amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginDepositTreasuryFeeDivisorUpdate

```solidity
function beginDepositTreasuryFeeDivisorUpdate(uint64 _newDepositTreasuryFeeDivisor) external
```

Begins the deposit treasury fee divisor amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDepositTreasuryFeeDivisor | uint64 | New deposit treasury fee divisor. |

### finalizeDepositTreasuryFeeDivisorUpdate

```solidity
function finalizeDepositTreasuryFeeDivisorUpdate() external
```

Finalizes the deposit treasury fee divisor amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginDepositTxMaxFeeUpdate

```solidity
function beginDepositTxMaxFeeUpdate(uint64 _newDepositTxMaxFee) external
```

Begins the deposit tx max fee amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDepositTxMaxFee | uint64 | New deposit tx max fee. |

### finalizeDepositTxMaxFeeUpdate

```solidity
function finalizeDepositTxMaxFeeUpdate() external
```

Finalizes the deposit tx max fee amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginDepositRevealAheadPeriodUpdate

```solidity
function beginDepositRevealAheadPeriodUpdate(uint32 _newDepositRevealAheadPeriod) external
```

Begins the deposit reveal ahead period update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDepositRevealAheadPeriod | uint32 | New deposit reveal ahead period. |

### finalizeDepositRevealAheadPeriodUpdate

```solidity
function finalizeDepositRevealAheadPeriodUpdate() external
```

Finalizes the deposit reveal ahead period update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRedemptionDustThresholdUpdate

```solidity
function beginRedemptionDustThresholdUpdate(uint64 _newRedemptionDustThreshold) external
```

Begins the redemption dust threshold amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRedemptionDustThreshold | uint64 | New redemption dust threshold. |

### finalizeRedemptionDustThresholdUpdate

```solidity
function finalizeRedemptionDustThresholdUpdate() external
```

Finalizes the dust threshold amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRedemptionTreasuryFeeDivisorUpdate

```solidity
function beginRedemptionTreasuryFeeDivisorUpdate(uint64 _newRedemptionTreasuryFeeDivisor) external
```

Begins the redemption treasury fee divisor amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRedemptionTreasuryFeeDivisor | uint64 | New redemption treasury fee divisor. |

### finalizeRedemptionTreasuryFeeDivisorUpdate

```solidity
function finalizeRedemptionTreasuryFeeDivisorUpdate() external
```

Finalizes the redemption treasury fee divisor amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRedemptionTxMaxFeeUpdate

```solidity
function beginRedemptionTxMaxFeeUpdate(uint64 _newRedemptionTxMaxFee) external
```

Begins the redemption tx max fee amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRedemptionTxMaxFee | uint64 | New redemption tx max fee. |

### finalizeRedemptionTxMaxFeeUpdate

```solidity
function finalizeRedemptionTxMaxFeeUpdate() external
```

Finalizes the redemption tx max fee amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRedemptionTxMaxTotalFeeUpdate

```solidity
function beginRedemptionTxMaxTotalFeeUpdate(uint64 _newRedemptionTxMaxTotalFee) external
```

Begins the redemption tx max total fee amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRedemptionTxMaxTotalFee | uint64 | New redemption tx max total fee. |

### finalizeRedemptionTxMaxTotalFeeUpdate

```solidity
function finalizeRedemptionTxMaxTotalFeeUpdate() external
```

Finalizes the redemption tx max total fee amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRedemptionTimeoutUpdate

```solidity
function beginRedemptionTimeoutUpdate(uint32 _newRedemptionTimeout) external
```

Begins the redemption timeout amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRedemptionTimeout | uint32 | New redemption timeout. |

### finalizeRedemptionTimeoutUpdate

```solidity
function finalizeRedemptionTimeoutUpdate() external
```

Finalizes the redemption timeout amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRedemptionTimeoutSlashingAmountUpdate

```solidity
function beginRedemptionTimeoutSlashingAmountUpdate(uint96 _newRedemptionTimeoutSlashingAmount) external
```

Begins the redemption timeout slashing amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRedemptionTimeoutSlashingAmount | uint96 | New redemption timeout slashing amount. |

### finalizeRedemptionTimeoutSlashingAmountUpdate

```solidity
function finalizeRedemptionTimeoutSlashingAmountUpdate() external
```

Finalizes the redemption timeout slashing amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRedemptionTimeoutNotifierRewardMultiplierUpdate

```solidity
function beginRedemptionTimeoutNotifierRewardMultiplierUpdate(uint32 _newRedemptionTimeoutNotifierRewardMultiplier) external
```

Begins the redemption timeout notifier reward multiplier amount
update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRedemptionTimeoutNotifierRewardMultiplier | uint32 | New redemption timeout notifier reward multiplier. |

### finalizeRedemptionTimeoutNotifierRewardMultiplierUpdate

```solidity
function finalizeRedemptionTimeoutNotifierRewardMultiplierUpdate() external
```

Finalizes the redemption timeout notifier reward multiplier amount
update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovingFundsTxMaxTotalFeeUpdate

```solidity
function beginMovingFundsTxMaxTotalFeeUpdate(uint64 _newMovingFundsTxMaxTotalFee) external
```

Begins the moving funds tx max total fee update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovingFundsTxMaxTotalFee | uint64 | New moving funds tx max total fee. |

### finalizeMovingFundsTxMaxTotalFeeUpdate

```solidity
function finalizeMovingFundsTxMaxTotalFeeUpdate() external
```

Finalizes the moving funds tx max total fee update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovingFundsDustThresholdUpdate

```solidity
function beginMovingFundsDustThresholdUpdate(uint64 _newMovingFundsDustThreshold) external
```

Begins the moving funds dust threshold update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovingFundsDustThreshold | uint64 | New moving funds dust threshold. |

### finalizeMovingFundsDustThresholdUpdate

```solidity
function finalizeMovingFundsDustThresholdUpdate() external
```

Finalizes the moving funds dust threshold update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovingFundsTimeoutResetDelayUpdate

```solidity
function beginMovingFundsTimeoutResetDelayUpdate(uint32 _newMovingFundsTimeoutResetDelay) external
```

Begins the moving funds timeout reset delay update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovingFundsTimeoutResetDelay | uint32 | New moving funds timeout reset delay. |

### finalizeMovingFundsTimeoutResetDelayUpdate

```solidity
function finalizeMovingFundsTimeoutResetDelayUpdate() external
```

Finalizes the moving funds timeout reset delay update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovingFundsTimeoutUpdate

```solidity
function beginMovingFundsTimeoutUpdate(uint32 _newMovingFundsTimeout) external
```

Begins the moving funds timeout update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovingFundsTimeout | uint32 | New moving funds timeout. |

### finalizeMovingFundsTimeoutUpdate

```solidity
function finalizeMovingFundsTimeoutUpdate() external
```

Finalizes the moving funds timeout update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovingFundsTimeoutSlashingAmountUpdate

```solidity
function beginMovingFundsTimeoutSlashingAmountUpdate(uint96 _newMovingFundsTimeoutSlashingAmount) external
```

Begins the moving funds timeout slashing amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovingFundsTimeoutSlashingAmount | uint96 | New moving funds timeout slashing amount. |

### finalizeMovingFundsTimeoutSlashingAmountUpdate

```solidity
function finalizeMovingFundsTimeoutSlashingAmountUpdate() external
```

Finalizes the moving funds timeout slashing amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovingFundsTimeoutNotifierRewardMultiplierUpdate

```solidity
function beginMovingFundsTimeoutNotifierRewardMultiplierUpdate(uint32 _newMovingFundsTimeoutNotifierRewardMultiplier) external
```

Begins the moving funds timeout notifier reward multiplier update
process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovingFundsTimeoutNotifierRewardMultiplier | uint32 | New moving funds timeout notifier reward multiplier. |

### finalizeMovingFundsTimeoutNotifierRewardMultiplierUpdate

```solidity
function finalizeMovingFundsTimeoutNotifierRewardMultiplierUpdate() external
```

Finalizes the moving funds timeout notifier reward multiplier
update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovingFundsCommitmentGasOffsetUpdate

```solidity
function beginMovingFundsCommitmentGasOffsetUpdate(uint16 _newMovingFundsCommitmentGasOffset) external
```

Begins the moving funds commitment gas offset update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovingFundsCommitmentGasOffset | uint16 | New moving funds commitment gas offset. |

### finalizeMovingFundsCommitmentGasOffsetUpdate

```solidity
function finalizeMovingFundsCommitmentGasOffsetUpdate() external
```

Finalizes the moving funds commitment gas offset update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovedFundsSweepTxMaxTotalFeeUpdate

```solidity
function beginMovedFundsSweepTxMaxTotalFeeUpdate(uint64 _newMovedFundsSweepTxMaxTotalFee) external
```

Begins the moved funds sweep tx max total fee update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovedFundsSweepTxMaxTotalFee | uint64 | New moved funds sweep tx max total fee. |

### finalizeMovedFundsSweepTxMaxTotalFeeUpdate

```solidity
function finalizeMovedFundsSweepTxMaxTotalFeeUpdate() external
```

Finalizes the moved funds sweep tx max total fee update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovedFundsSweepTimeoutUpdate

```solidity
function beginMovedFundsSweepTimeoutUpdate(uint32 _newMovedFundsSweepTimeout) external
```

Begins the moved funds sweep timeout update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovedFundsSweepTimeout | uint32 | New moved funds sweep timeout. |

### finalizeMovedFundsSweepTimeoutUpdate

```solidity
function finalizeMovedFundsSweepTimeoutUpdate() external
```

Finalizes the moved funds sweep timeout update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovedFundsSweepTimeoutSlashingAmountUpdate

```solidity
function beginMovedFundsSweepTimeoutSlashingAmountUpdate(uint96 _newMovedFundsSweepTimeoutSlashingAmount) external
```

Begins the moved funds sweep timeout slashing amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovedFundsSweepTimeoutSlashingAmount | uint96 | New moved funds sweep timeout slashing amount. |

### finalizeMovedFundsSweepTimeoutSlashingAmountUpdate

```solidity
function finalizeMovedFundsSweepTimeoutSlashingAmountUpdate() external
```

Finalizes the moved funds sweep timeout slashing amount update
process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate

```solidity
function beginMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate(uint32 _newMovedFundsSweepTimeoutNotifierRewardMultiplier) external
```

Begins the moved funds sweep timeout notifier reward multiplier
update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovedFundsSweepTimeoutNotifierRewardMultiplier | uint32 | New moved funds sweep timeout notifier reward multiplier. |

### finalizeMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate

```solidity
function finalizeMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate() external
```

Finalizes the moved funds sweep timeout notifier reward multiplier
update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginWalletCreationPeriodUpdate

```solidity
function beginWalletCreationPeriodUpdate(uint32 _newWalletCreationPeriod) external
```

Begins the wallet creation period update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newWalletCreationPeriod | uint32 | New wallet creation period. |

### finalizeWalletCreationPeriodUpdate

```solidity
function finalizeWalletCreationPeriodUpdate() external
```

Finalizes the wallet creation period update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginWalletCreationMinBtcBalanceUpdate

```solidity
function beginWalletCreationMinBtcBalanceUpdate(uint64 _newWalletCreationMinBtcBalance) external
```

Begins the wallet creation min btc balance update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newWalletCreationMinBtcBalance | uint64 | New wallet creation min btc balance. |

### finalizeWalletCreationMinBtcBalanceUpdate

```solidity
function finalizeWalletCreationMinBtcBalanceUpdate() external
```

Finalizes the wallet creation min btc balance update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginWalletCreationMaxBtcBalanceUpdate

```solidity
function beginWalletCreationMaxBtcBalanceUpdate(uint64 _newWalletCreationMaxBtcBalance) external
```

Begins the wallet creation max btc balance update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newWalletCreationMaxBtcBalance | uint64 | New wallet creation max btc balance. |

### finalizeWalletCreationMaxBtcBalanceUpdate

```solidity
function finalizeWalletCreationMaxBtcBalanceUpdate() external
```

Finalizes the wallet creation max btc balance update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginWalletClosureMinBtcBalanceUpdate

```solidity
function beginWalletClosureMinBtcBalanceUpdate(uint64 _newWalletClosureMinBtcBalance) external
```

Begins the wallet closure min btc balance update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newWalletClosureMinBtcBalance | uint64 | New wallet closure min btc balance. |

### finalizeWalletClosureMinBtcBalanceUpdate

```solidity
function finalizeWalletClosureMinBtcBalanceUpdate() external
```

Finalizes the wallet closure min btc balance update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginWalletMaxAgeUpdate

```solidity
function beginWalletMaxAgeUpdate(uint32 _newWalletMaxAge) external
```

Begins the wallet max age update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newWalletMaxAge | uint32 | New wallet max age. |

### finalizeWalletMaxAgeUpdate

```solidity
function finalizeWalletMaxAgeUpdate() external
```

Finalizes the wallet max age update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginWalletMaxBtcTransferUpdate

```solidity
function beginWalletMaxBtcTransferUpdate(uint64 _newWalletMaxBtcTransfer) external
```

Begins the wallet max btc transfer amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newWalletMaxBtcTransfer | uint64 | New wallet max btc transfer. |

### finalizeWalletMaxBtcTransferUpdate

```solidity
function finalizeWalletMaxBtcTransferUpdate() external
```

Finalizes the wallet max btc transfer amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginWalletClosingPeriodUpdate

```solidity
function beginWalletClosingPeriodUpdate(uint32 _newWalletClosingPeriod) external
```

Begins the wallet closing period update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newWalletClosingPeriod | uint32 | New wallet closing period. |

### finalizeWalletClosingPeriodUpdate

```solidity
function finalizeWalletClosingPeriodUpdate() external
```

Finalizes the wallet closing period update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginFraudChallengeDepositAmountUpdate

```solidity
function beginFraudChallengeDepositAmountUpdate(uint96 _newFraudChallengeDepositAmount) external
```

Begins the fraud challenge deposit amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newFraudChallengeDepositAmount | uint96 | New fraud challenge deposit amount. |

### finalizeFraudChallengeDepositAmountUpdate

```solidity
function finalizeFraudChallengeDepositAmountUpdate() external
```

Finalizes the fraud challenge deposit amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginFraudChallengeDefeatTimeoutUpdate

```solidity
function beginFraudChallengeDefeatTimeoutUpdate(uint32 _newFraudChallengeDefeatTimeout) external
```

Begins the fraud challenge defeat timeout update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newFraudChallengeDefeatTimeout | uint32 | New fraud challenge defeat timeout. |

### finalizeFraudChallengeDefeatTimeoutUpdate

```solidity
function finalizeFraudChallengeDefeatTimeoutUpdate() external
```

Finalizes the fraud challenge defeat timeout update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginFraudSlashingAmountUpdate

```solidity
function beginFraudSlashingAmountUpdate(uint96 _newFraudSlashingAmount) external
```

Begins the fraud slashing amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newFraudSlashingAmount | uint96 | New fraud slashing amount. |

### finalizeFraudSlashingAmountUpdate

```solidity
function finalizeFraudSlashingAmountUpdate() external
```

Finalizes the fraud slashing amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginFraudNotifierRewardMultiplierUpdate

```solidity
function beginFraudNotifierRewardMultiplierUpdate(uint32 _newFraudNotifierRewardMultiplier) external
```

Begins the fraud notifier reward multiplier update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newFraudNotifierRewardMultiplier | uint32 | New fraud notifier reward multiplier. |

### finalizeFraudNotifierRewardMultiplierUpdate

```solidity
function finalizeFraudNotifierRewardMultiplierUpdate() external
```

Finalizes the fraud notifier reward multiplier update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginTreasuryUpdate

```solidity
function beginTreasuryUpdate(address _newTreasury) external
```

Begins the treasury address update process.

Can be called only by the contract owner. It does not perform
any parameter validation.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newTreasury | address | New treasury address. |

### finalizeTreasuryUpdate

```solidity
function finalizeTreasuryUpdate() external
```

Finalizes the treasury address update process.

Can be called only by the contract owner, after the governance
delay elapses.

### governanceDelay

```solidity
function governanceDelay() internal view returns (uint256)
```

Gets the governance delay parameter.

