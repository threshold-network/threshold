# Solidity API

## BridgeGovernanceParameters

### TreasuryData

```solidity
struct TreasuryData {
  address newTreasury;
  uint256 treasuryChangeInitiated;
}
```

### DepositData

```solidity
struct DepositData {
  uint64 newDepositDustThreshold;
  uint256 depositDustThresholdChangeInitiated;
  uint64 newDepositTreasuryFeeDivisor;
  uint256 depositTreasuryFeeDivisorChangeInitiated;
  uint64 newDepositTxMaxFee;
  uint256 depositTxMaxFeeChangeInitiated;
  uint32 newDepositRevealAheadPeriod;
  uint256 depositRevealAheadPeriodChangeInitiated;
}
```

### RedemptionData

```solidity
struct RedemptionData {
  uint64 newRedemptionDustThreshold;
  uint256 redemptionDustThresholdChangeInitiated;
  uint64 newRedemptionTreasuryFeeDivisor;
  uint256 redemptionTreasuryFeeDivisorChangeInitiated;
  uint64 newRedemptionTxMaxFee;
  uint256 redemptionTxMaxFeeChangeInitiated;
  uint64 newRedemptionTxMaxTotalFee;
  uint256 redemptionTxMaxTotalFeeChangeInitiated;
  uint32 newRedemptionTimeout;
  uint256 redemptionTimeoutChangeInitiated;
  uint96 newRedemptionTimeoutSlashingAmount;
  uint256 redemptionTimeoutSlashingAmountChangeInitiated;
  uint32 newRedemptionTimeoutNotifierRewardMultiplier;
  uint256 redemptionTimeoutNotifierRewardMultiplierChangeInitiated;
}
```

### MovingFundsData

```solidity
struct MovingFundsData {
  uint64 newMovingFundsTxMaxTotalFee;
  uint256 movingFundsTxMaxTotalFeeChangeInitiated;
  uint64 newMovingFundsDustThreshold;
  uint256 movingFundsDustThresholdChangeInitiated;
  uint32 newMovingFundsTimeoutResetDelay;
  uint256 movingFundsTimeoutResetDelayChangeInitiated;
  uint32 newMovingFundsTimeout;
  uint256 movingFundsTimeoutChangeInitiated;
  uint96 newMovingFundsTimeoutSlashingAmount;
  uint256 movingFundsTimeoutSlashingAmountChangeInitiated;
  uint32 newMovingFundsTimeoutNotifierRewardMultiplier;
  uint256 movingFundsTimeoutNotifierRewardMultiplierChangeInitiated;
  uint16 newMovingFundsCommitmentGasOffset;
  uint256 movingFundsCommitmentGasOffsetChangeInitiated;
  uint64 newMovedFundsSweepTxMaxTotalFee;
  uint256 movedFundsSweepTxMaxTotalFeeChangeInitiated;
  uint32 newMovedFundsSweepTimeout;
  uint256 movedFundsSweepTimeoutChangeInitiated;
  uint96 newMovedFundsSweepTimeoutSlashingAmount;
  uint256 movedFundsSweepTimeoutSlashingAmountChangeInitiated;
  uint32 newMovedFundsSweepTimeoutNotifierRewardMultiplier;
  uint256 movedFundsSweepTimeoutNotifierRewardMultiplierChangeInitiated;
}
```

### WalletData

```solidity
struct WalletData {
  uint32 newWalletCreationPeriod;
  uint256 walletCreationPeriodChangeInitiated;
  uint64 newWalletCreationMinBtcBalance;
  uint256 walletCreationMinBtcBalanceChangeInitiated;
  uint64 newWalletCreationMaxBtcBalance;
  uint256 walletCreationMaxBtcBalanceChangeInitiated;
  uint64 newWalletClosureMinBtcBalance;
  uint256 walletClosureMinBtcBalanceChangeInitiated;
  uint32 newWalletMaxAge;
  uint256 walletMaxAgeChangeInitiated;
  uint64 newWalletMaxBtcTransfer;
  uint256 walletMaxBtcTransferChangeInitiated;
  uint32 newWalletClosingPeriod;
  uint256 walletClosingPeriodChangeInitiated;
}
```

### FraudData

```solidity
struct FraudData {
  uint96 newFraudChallengeDepositAmount;
  uint256 fraudChallengeDepositAmountChangeInitiated;
  uint32 newFraudChallengeDefeatTimeout;
  uint256 fraudChallengeDefeatTimeoutChangeInitiated;
  uint96 newFraudSlashingAmount;
  uint256 fraudSlashingAmountChangeInitiated;
  uint32 newFraudNotifierRewardMultiplier;
  uint256 fraudNotifierRewardMultiplierChangeInitiated;
}
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

### onlyAfterGovernanceDelay

```solidity
modifier onlyAfterGovernanceDelay(uint256 changeInitiatedTimestamp, uint256 governanceDelay)
```

Reverts if called before the governance delay elapses.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| changeInitiatedTimestamp | uint256 | Timestamp indicating the beginning of the change. |
| governanceDelay | uint256 |  |

### beginDepositDustThresholdUpdate

```solidity
function beginDepositDustThresholdUpdate(struct BridgeGovernanceParameters.DepositData self, uint64 _newDepositDustThreshold) external
```

Begins the deposit dust threshold amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.DepositData |  |
| _newDepositDustThreshold | uint64 | New deposit dust threshold amount. |

### finalizeDepositDustThresholdUpdate

```solidity
function finalizeDepositDustThresholdUpdate(struct BridgeGovernanceParameters.DepositData self, uint256 governanceDelay) external
```

Finalizes the deposit dust threshold amount update process.

Can be called after the governance delay elapses.

### beginDepositTreasuryFeeDivisorUpdate

```solidity
function beginDepositTreasuryFeeDivisorUpdate(struct BridgeGovernanceParameters.DepositData self, uint64 _newDepositTreasuryFeeDivisor) external
```

Begins the deposit treasury fee divisor amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.DepositData |  |
| _newDepositTreasuryFeeDivisor | uint64 | New deposit treasury fee divisor amount. |

### finalizeDepositTreasuryFeeDivisorUpdate

```solidity
function finalizeDepositTreasuryFeeDivisorUpdate(struct BridgeGovernanceParameters.DepositData self, uint256 governanceDelay) external
```

Finalizes the deposit treasury fee divisor amount update process.

Can be called after the governance delay elapses.

### beginDepositTxMaxFeeUpdate

```solidity
function beginDepositTxMaxFeeUpdate(struct BridgeGovernanceParameters.DepositData self, uint64 _newDepositTxMaxFee) external
```

Begins the deposit tx max fee amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.DepositData |  |
| _newDepositTxMaxFee | uint64 | New deposit tx max fee amount. |

### finalizeDepositTxMaxFeeUpdate

```solidity
function finalizeDepositTxMaxFeeUpdate(struct BridgeGovernanceParameters.DepositData self, uint256 governanceDelay) external
```

Finalizes the deposit tx max fee amount update process.

Can be called after the governance delay elapses.

### beginDepositRevealAheadPeriodUpdate

```solidity
function beginDepositRevealAheadPeriodUpdate(struct BridgeGovernanceParameters.DepositData self, uint32 _newDepositRevealAheadPeriod) external
```

Begins the deposit reveal ahead period update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.DepositData |  |
| _newDepositRevealAheadPeriod | uint32 | New deposit reveal ahead period. |

### finalizeDepositRevealAheadPeriodUpdate

```solidity
function finalizeDepositRevealAheadPeriodUpdate(struct BridgeGovernanceParameters.DepositData self, uint256 governanceDelay) external
```

Finalizes the deposit reveal ahead period update process.

Can be called after the governance delay elapses.

### beginRedemptionDustThresholdUpdate

```solidity
function beginRedemptionDustThresholdUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint64 _newRedemptionDustThreshold) external
```

Begins the redemption dust threshold amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.RedemptionData |  |
| _newRedemptionDustThreshold | uint64 | New redemption dust threshold amount. |

### finalizeRedemptionDustThresholdUpdate

```solidity
function finalizeRedemptionDustThresholdUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint256 governanceDelay) external
```

Finalizes the redemption dust threshold amount update process.

Can be called after the governance delay elapses.

### beginRedemptionTreasuryFeeDivisorUpdate

```solidity
function beginRedemptionTreasuryFeeDivisorUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint64 _newRedemptionTreasuryFeeDivisor) external
```

Begins the redemption treasury fee divisor amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.RedemptionData |  |
| _newRedemptionTreasuryFeeDivisor | uint64 | New redemption treasury fee divisor amount. |

### finalizeRedemptionTreasuryFeeDivisorUpdate

```solidity
function finalizeRedemptionTreasuryFeeDivisorUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint256 governanceDelay) external
```

Finalizes the redemption treasury fee divisor amount update process.

Can be called after the governance delay elapses.

### beginRedemptionTxMaxFeeUpdate

```solidity
function beginRedemptionTxMaxFeeUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint64 _newRedemptionTxMaxFee) external
```

Begins the redemption tx max fee amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.RedemptionData |  |
| _newRedemptionTxMaxFee | uint64 | New redemption tx max fee amount. |

### finalizeRedemptionTxMaxFeeUpdate

```solidity
function finalizeRedemptionTxMaxFeeUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint256 governanceDelay) external
```

Finalizes the redemption tx max fee amount update process.

Can be called after the governance delay elapses.

### beginRedemptionTxMaxTotalFeeUpdate

```solidity
function beginRedemptionTxMaxTotalFeeUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint64 _newRedemptionTxMaxTotalFee) external
```

Begins the redemption tx max total fee amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.RedemptionData |  |
| _newRedemptionTxMaxTotalFee | uint64 | New redemption tx max total fee amount. |

### finalizeRedemptionTxMaxTotalFeeUpdate

```solidity
function finalizeRedemptionTxMaxTotalFeeUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint256 governanceDelay) external
```

Finalizes the redemption tx max total fee amount update process.

Can be called after the governance delay elapses.

### beginRedemptionTimeoutUpdate

```solidity
function beginRedemptionTimeoutUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint32 _newRedemptionTimeout) external
```

Begins the redemption timeout amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.RedemptionData |  |
| _newRedemptionTimeout | uint32 | New redemption timeout amount. |

### finalizeRedemptionTimeoutUpdate

```solidity
function finalizeRedemptionTimeoutUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint256 governanceDelay) external
```

Finalizes the redemption timeout amount update
process.

Can be called after the governance delay elapses.

### beginRedemptionTimeoutSlashingAmountUpdate

```solidity
function beginRedemptionTimeoutSlashingAmountUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint96 _newRedemptionTimeoutSlashingAmount) external
```

Begins the redemption timeout slashing amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.RedemptionData |  |
| _newRedemptionTimeoutSlashingAmount | uint96 | New redemption timeout slashing amount. |

### finalizeRedemptionTimeoutSlashingAmountUpdate

```solidity
function finalizeRedemptionTimeoutSlashingAmountUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint256 governanceDelay) external
```

Finalizes the redemption timeout slashing amount update process.

Can be called after the governance delay elapses.

### beginRedemptionTimeoutNotifierRewardMultiplierUpdate

```solidity
function beginRedemptionTimeoutNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint32 _newRedemptionTimeoutNotifierRewardMultiplier) external
```

Begins the redemption timeout notifier reward multiplier amount
update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.RedemptionData |  |
| _newRedemptionTimeoutNotifierRewardMultiplier | uint32 | New redemption timeout notifier reward multiplier amount. |

### finalizeRedemptionTimeoutNotifierRewardMultiplierUpdate

```solidity
function finalizeRedemptionTimeoutNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint256 governanceDelay) external
```

Finalizes the redemption timeout notifier reward multiplier amount update process.

Can be called after the governance delay elapses.

### beginMovingFundsTxMaxTotalFeeUpdate

```solidity
function beginMovingFundsTxMaxTotalFeeUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint64 _newMovingFundsTxMaxTotalFee) external
```

Begins the moving funds tx max total fee amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovingFundsTxMaxTotalFee | uint64 | New moving funds tx max total fee amount. |

### finalizeMovingFundsTxMaxTotalFeeUpdate

```solidity
function finalizeMovingFundsTxMaxTotalFeeUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moving funds tx max total fee amount update process.

Can be called after the governance delay elapses.

### beginMovingFundsDustThresholdUpdate

```solidity
function beginMovingFundsDustThresholdUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint64 _newMovingFundsDustThreshold) external
```

Begins the moving funds dust threshold amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovingFundsDustThreshold | uint64 | New moving funds dust threshold amount. |

### finalizeMovingFundsDustThresholdUpdate

```solidity
function finalizeMovingFundsDustThresholdUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moving funds dust threshold amount update process.

Can be called after the governance delay elapses.

### beginMovingFundsTimeoutResetDelayUpdate

```solidity
function beginMovingFundsTimeoutResetDelayUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint32 _newMovingFundsTimeoutResetDelay) external
```

Begins the moving funds timeout reset delay amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovingFundsTimeoutResetDelay | uint32 | New moving funds timeout reset delay amount. |

### finalizeMovingFundsTimeoutResetDelayUpdate

```solidity
function finalizeMovingFundsTimeoutResetDelayUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moving funds timeout reset delay amount update process.

Can be called after the governance delay elapses.

### beginMovingFundsTimeoutUpdate

```solidity
function beginMovingFundsTimeoutUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint32 _newMovingFundsTimeout) external
```

Begins the moving funds timeout amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovingFundsTimeout | uint32 | New moving funds timeout amount. |

### finalizeMovingFundsTimeoutUpdate

```solidity
function finalizeMovingFundsTimeoutUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moving funds timeout amount update process.

Can be called after the governance delay elapses.

### beginMovingFundsTimeoutSlashingAmountUpdate

```solidity
function beginMovingFundsTimeoutSlashingAmountUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint96 _newMovingFundsTimeoutSlashingAmount) external
```

Begins the moving funds timeout slashing amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovingFundsTimeoutSlashingAmount | uint96 | New moving funds timeout slashing amount. |

### finalizeMovingFundsTimeoutSlashingAmountUpdate

```solidity
function finalizeMovingFundsTimeoutSlashingAmountUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moving funds timeout slashing amount update process.

Can be called after the governance delay elapses.

### beginMovingFundsTimeoutNotifierRewardMultiplierUpdate

```solidity
function beginMovingFundsTimeoutNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint32 _newMovingFundsTimeoutNotifierRewardMultiplier) external
```

Begins the moving funds timeout notifier reward multiplier amount
update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovingFundsTimeoutNotifierRewardMultiplier | uint32 | New moving funds timeout notifier reward multiplier amount. |

### finalizeMovingFundsTimeoutNotifierRewardMultiplierUpdate

```solidity
function finalizeMovingFundsTimeoutNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moving funds timeout notifier reward multiplier
amount update process.

Can be called after the governance delay elapses.

### beginMovingFundsCommitmentGasOffsetUpdate

```solidity
function beginMovingFundsCommitmentGasOffsetUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint16 _newMovingFundsCommitmentGasOffset) external
```

Begins the moving funds commitment gas offset update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovingFundsCommitmentGasOffset | uint16 | New moving funds commitment gas offset. |

### finalizeMovingFundsCommitmentGasOffsetUpdate

```solidity
function finalizeMovingFundsCommitmentGasOffsetUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moving funds commitment gas offset update process.

Can be called after the governance delay elapses.

### beginMovedFundsSweepTxMaxTotalFeeUpdate

```solidity
function beginMovedFundsSweepTxMaxTotalFeeUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint64 _newMovedFundsSweepTxMaxTotalFee) external
```

Begins the moved funds sweep tx max total fee amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovedFundsSweepTxMaxTotalFee | uint64 | New moved funds sweep tx max total fee amount. |

### finalizeMovedFundsSweepTxMaxTotalFeeUpdate

```solidity
function finalizeMovedFundsSweepTxMaxTotalFeeUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moved funds sweep tx max total fee amount update
process.

Can be called after the governance delay elapses.

### beginMovedFundsSweepTimeoutUpdate

```solidity
function beginMovedFundsSweepTimeoutUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint32 _newMovedFundsSweepTimeout) external
```

Begins the moved funds sweep timeout amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovedFundsSweepTimeout | uint32 | New moved funds sweep timeout amount. |

### finalizeMovedFundsSweepTimeoutUpdate

```solidity
function finalizeMovedFundsSweepTimeoutUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moved funds sweep timeout amount update process.

Can be called after the governance delay elapses.

### beginMovedFundsSweepTimeoutSlashingAmountUpdate

```solidity
function beginMovedFundsSweepTimeoutSlashingAmountUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint96 _newMovedFundsSweepTimeoutSlashingAmount) external
```

Begins the moved funds sweep timeout slashing amount update
process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovedFundsSweepTimeoutSlashingAmount | uint96 | New moved funds sweep timeout slashing amount. |

### finalizeMovedFundsSweepTimeoutSlashingAmountUpdate

```solidity
function finalizeMovedFundsSweepTimeoutSlashingAmountUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moved funds sweep timeout slashing amount
update process.

Can be called after the governance delay elapses.

### beginMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate

```solidity
function beginMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint32 _newMovedFundsSweepTimeoutNotifierRewardMultiplier) external
```

Begins the moved funds sweep timeout notifier reward multiplier
amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovedFundsSweepTimeoutNotifierRewardMultiplier | uint32 | New moved funds sweep timeout notifier reward multiplier amount. |

### finalizeMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate

```solidity
function finalizeMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moved funds sweep timeout notifier reward multiplier
amount update process.

Can be called after the governance delay elapses.

### beginWalletCreationPeriodUpdate

```solidity
function beginWalletCreationPeriodUpdate(struct BridgeGovernanceParameters.WalletData self, uint32 _newWalletCreationPeriod) external
```

Begins the wallet creation period amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.WalletData |  |
| _newWalletCreationPeriod | uint32 | New wallet creation period amount. |

### finalizeWalletCreationPeriodUpdate

```solidity
function finalizeWalletCreationPeriodUpdate(struct BridgeGovernanceParameters.WalletData self, uint256 governanceDelay) external
```

Finalizes the wallet creation period amount update process.

Can be called after the governance delay elapses.

### beginWalletCreationMinBtcBalanceUpdate

```solidity
function beginWalletCreationMinBtcBalanceUpdate(struct BridgeGovernanceParameters.WalletData self, uint64 _newWalletCreationMinBtcBalance) external
```

Begins the wallet creation min btc balance amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.WalletData |  |
| _newWalletCreationMinBtcBalance | uint64 | New wallet creation min btc balance amount. |

### finalizeWalletCreationMinBtcBalanceUpdate

```solidity
function finalizeWalletCreationMinBtcBalanceUpdate(struct BridgeGovernanceParameters.WalletData self, uint256 governanceDelay) external
```

Finalizes the wallet creation min btc balance amount update process.

Can be called after the governance delay elapses.

### beginWalletCreationMaxBtcBalanceUpdate

```solidity
function beginWalletCreationMaxBtcBalanceUpdate(struct BridgeGovernanceParameters.WalletData self, uint64 _newWalletCreationMaxBtcBalance) external
```

Begins the wallet creation max btc balance amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.WalletData |  |
| _newWalletCreationMaxBtcBalance | uint64 | New wallet creation max btc balance amount. |

### finalizeWalletCreationMaxBtcBalanceUpdate

```solidity
function finalizeWalletCreationMaxBtcBalanceUpdate(struct BridgeGovernanceParameters.WalletData self, uint256 governanceDelay) external
```

Finalizes the wallet creation max btc balance amount update process.

Can be called after the governance delay elapses.

### beginWalletClosureMinBtcBalanceUpdate

```solidity
function beginWalletClosureMinBtcBalanceUpdate(struct BridgeGovernanceParameters.WalletData self, uint64 _newWalletClosureMinBtcBalance) external
```

Begins the wallet closure min btc balance amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.WalletData |  |
| _newWalletClosureMinBtcBalance | uint64 | New wallet closure min btc balance amount. |

### finalizeWalletClosureMinBtcBalanceUpdate

```solidity
function finalizeWalletClosureMinBtcBalanceUpdate(struct BridgeGovernanceParameters.WalletData self, uint256 governanceDelay) external
```

Finalizes the wallet closure min btc balance amount update process.

Can be called after the governance delay elapses.

### beginWalletMaxAgeUpdate

```solidity
function beginWalletMaxAgeUpdate(struct BridgeGovernanceParameters.WalletData self, uint32 _newWalletMaxAge) external
```

Begins the wallet max age amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.WalletData |  |
| _newWalletMaxAge | uint32 | New wallet max age amount. |

### finalizeWalletMaxAgeUpdate

```solidity
function finalizeWalletMaxAgeUpdate(struct BridgeGovernanceParameters.WalletData self, uint256 governanceDelay) external
```

Finalizes the wallet max age amount update process.

Can be called after the governance delay elapses.

### beginWalletMaxBtcTransferUpdate

```solidity
function beginWalletMaxBtcTransferUpdate(struct BridgeGovernanceParameters.WalletData self, uint64 _newWalletMaxBtcTransfer) external
```

Begins the wallet max btc transfer amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.WalletData |  |
| _newWalletMaxBtcTransfer | uint64 | New wallet max btc transfer amount. |

### finalizeWalletMaxBtcTransferUpdate

```solidity
function finalizeWalletMaxBtcTransferUpdate(struct BridgeGovernanceParameters.WalletData self, uint256 governanceDelay) external
```

Finalizes the wallet max btc transfer amount update process.

Can be called after the governance delay elapses.

### beginWalletClosingPeriodUpdate

```solidity
function beginWalletClosingPeriodUpdate(struct BridgeGovernanceParameters.WalletData self, uint32 _newWalletClosingPeriod) external
```

Begins the wallet closing period amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.WalletData |  |
| _newWalletClosingPeriod | uint32 | New wallet closing period amount. |

### finalizeWalletClosingPeriodUpdate

```solidity
function finalizeWalletClosingPeriodUpdate(struct BridgeGovernanceParameters.WalletData self, uint256 governanceDelay) external
```

Finalizes the wallet closing period amount update process.

Can be called after the governance delay elapses.

### beginFraudChallengeDepositAmountUpdate

```solidity
function beginFraudChallengeDepositAmountUpdate(struct BridgeGovernanceParameters.FraudData self, uint96 _newFraudChallengeDepositAmount) external
```

Begins the fraud challenge deposit amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.FraudData |  |
| _newFraudChallengeDepositAmount | uint96 | New fraud challenge deposit amount. |

### finalizeFraudChallengeDepositAmountUpdate

```solidity
function finalizeFraudChallengeDepositAmountUpdate(struct BridgeGovernanceParameters.FraudData self, uint256 governanceDelay) external
```

Finalizes the fraud challenge deposit amount update process.

Can be called after the governance delay elapses.

### beginFraudChallengeDefeatTimeoutUpdate

```solidity
function beginFraudChallengeDefeatTimeoutUpdate(struct BridgeGovernanceParameters.FraudData self, uint32 _newFraudChallengeDefeatTimeout) external
```

Begins the fraud challenge defeat timeout amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.FraudData |  |
| _newFraudChallengeDefeatTimeout | uint32 | New fraud challenge defeat timeout amount. |

### finalizeFraudChallengeDefeatTimeoutUpdate

```solidity
function finalizeFraudChallengeDefeatTimeoutUpdate(struct BridgeGovernanceParameters.FraudData self, uint256 governanceDelay) external
```

Finalizes the fraud challenge defeat timeout amount update process.

Can be called after the governance delay elapses.

### beginFraudSlashingAmountUpdate

```solidity
function beginFraudSlashingAmountUpdate(struct BridgeGovernanceParameters.FraudData self, uint96 _newFraudSlashingAmount) external
```

Begins the fraud slashing amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.FraudData |  |
| _newFraudSlashingAmount | uint96 | New fraud slashing amount. |

### finalizeFraudSlashingAmountUpdate

```solidity
function finalizeFraudSlashingAmountUpdate(struct BridgeGovernanceParameters.FraudData self, uint256 governanceDelay) external
```

Finalizes the fraud slashing amount update process.

Can be called after the governance delay elapses.

### beginFraudNotifierRewardMultiplierUpdate

```solidity
function beginFraudNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.FraudData self, uint32 _newFraudNotifierRewardMultiplier) external
```

Begins the fraud notifier reward multiplier amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.FraudData |  |
| _newFraudNotifierRewardMultiplier | uint32 | New fraud notifier reward multiplier amount. |

### finalizeFraudNotifierRewardMultiplierUpdate

```solidity
function finalizeFraudNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.FraudData self, uint256 governanceDelay) external
```

Finalizes the fraud notifier reward multiplier amount update process.

Can be called after the governance delay elapses.

### beginTreasuryUpdate

```solidity
function beginTreasuryUpdate(struct BridgeGovernanceParameters.TreasuryData self, address _newTreasury) external
```

Begins the treasury address update process.

It does not perform any parameter validation.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.TreasuryData |  |
| _newTreasury | address | New treasury address. |

### finalizeTreasuryUpdate

```solidity
function finalizeTreasuryUpdate(struct BridgeGovernanceParameters.TreasuryData self, uint256 governanceDelay) external
```

Finalizes the treasury address update process.

Can be called after the governance delay elapses.

