# Solidity API

## BridgeState

### Storage

```solidity
struct Storage {
  contract Bank bank;
  contract IRelay relay;
  uint96 txProofDifficultyFactor;
  contract IWalletRegistry ecdsaWalletRegistry;
  contract ReimbursementPool reimbursementPool;
  address treasury;
  bytes32 __treasuryAlignmentGap;
  uint64 depositDustThreshold;
  uint64 depositTreasuryFeeDivisor;
  uint64 depositTxMaxFee;
  uint32 depositRevealAheadPeriod;
  bytes32 __depositAlignmentGap;
  uint64 movingFundsTxMaxTotalFee;
  uint64 movingFundsDustThreshold;
  uint32 movingFundsTimeoutResetDelay;
  uint32 movingFundsTimeout;
  uint96 movingFundsTimeoutSlashingAmount;
  uint32 movingFundsTimeoutNotifierRewardMultiplier;
  uint16 movingFundsCommitmentGasOffset;
  bytes32 __movingFundsAlignmentGap;
  uint64 movedFundsSweepTxMaxTotalFee;
  uint32 movedFundsSweepTimeout;
  uint96 movedFundsSweepTimeoutSlashingAmount;
  uint32 movedFundsSweepTimeoutNotifierRewardMultiplier;
  uint64 redemptionDustThreshold;
  uint64 redemptionTreasuryFeeDivisor;
  uint64 redemptionTxMaxFee;
  uint64 redemptionTxMaxTotalFee;
  bytes32 __redemptionAlignmentGap;
  uint32 redemptionTimeout;
  uint96 redemptionTimeoutSlashingAmount;
  uint32 redemptionTimeoutNotifierRewardMultiplier;
  uint96 fraudChallengeDepositAmount;
  uint32 fraudChallengeDefeatTimeout;
  uint96 fraudSlashingAmount;
  uint32 fraudNotifierRewardMultiplier;
  uint32 walletCreationPeriod;
  uint64 walletCreationMinBtcBalance;
  uint64 walletCreationMaxBtcBalance;
  uint64 walletClosureMinBtcBalance;
  uint32 walletMaxAge;
  bytes20 activeWalletPubKeyHash;
  uint32 liveWalletsCount;
  uint64 walletMaxBtcTransfer;
  uint32 walletClosingPeriod;
  mapping(uint256 => struct Deposit.DepositRequest) deposits;
  mapping(address => bool) isVaultTrusted;
  mapping(address => bool) isSpvMaintainer;
  mapping(uint256 => struct MovingFunds.MovedFundsSweepRequest) movedFundsSweepRequests;
  mapping(uint256 => struct Redemption.RedemptionRequest) pendingRedemptions;
  mapping(uint256 => struct Redemption.RedemptionRequest) timedOutRedemptions;
  mapping(uint256 => struct Fraud.FraudChallenge) fraudChallenges;
  mapping(uint256 => bool) spentMainUTXOs;
  mapping(bytes20 => struct Wallets.Wallet) registeredWallets;
  uint256[50] __gap;
}
```

### DepositParametersUpdated

```solidity
event DepositParametersUpdated(uint64 depositDustThreshold, uint64 depositTreasuryFeeDivisor, uint64 depositTxMaxFee, uint32 depositRevealAheadPeriod)
```

### RedemptionParametersUpdated

```solidity
event RedemptionParametersUpdated(uint64 redemptionDustThreshold, uint64 redemptionTreasuryFeeDivisor, uint64 redemptionTxMaxFee, uint64 redemptionTxMaxTotalFee, uint32 redemptionTimeout, uint96 redemptionTimeoutSlashingAmount, uint32 redemptionTimeoutNotifierRewardMultiplier)
```

### MovingFundsParametersUpdated

```solidity
event MovingFundsParametersUpdated(uint64 movingFundsTxMaxTotalFee, uint64 movingFundsDustThreshold, uint32 movingFundsTimeoutResetDelay, uint32 movingFundsTimeout, uint96 movingFundsTimeoutSlashingAmount, uint32 movingFundsTimeoutNotifierRewardMultiplier, uint16 movingFundsCommitmentGasOffset, uint64 movedFundsSweepTxMaxTotalFee, uint32 movedFundsSweepTimeout, uint96 movedFundsSweepTimeoutSlashingAmount, uint32 movedFundsSweepTimeoutNotifierRewardMultiplier)
```

### WalletParametersUpdated

```solidity
event WalletParametersUpdated(uint32 walletCreationPeriod, uint64 walletCreationMinBtcBalance, uint64 walletCreationMaxBtcBalance, uint64 walletClosureMinBtcBalance, uint32 walletMaxAge, uint64 walletMaxBtcTransfer, uint32 walletClosingPeriod)
```

### FraudParametersUpdated

```solidity
event FraudParametersUpdated(uint96 fraudChallengeDepositAmount, uint32 fraudChallengeDefeatTimeout, uint96 fraudSlashingAmount, uint32 fraudNotifierRewardMultiplier)
```

### TreasuryUpdated

```solidity
event TreasuryUpdated(address treasury)
```

### updateDepositParameters

```solidity
function updateDepositParameters(struct BridgeState.Storage self, uint64 _depositDustThreshold, uint64 _depositTreasuryFeeDivisor, uint64 _depositTxMaxFee, uint32 _depositRevealAheadPeriod) internal
```

of `depositTreasuryFeeDivisor` and `depositTxMaxFee` parameters
in order to make requests that can incur the treasury and
transaction fee and still satisfy the depositor.

Requirements:
- Deposit dust threshold must be greater than zero,
- Deposit dust threshold must be greater than deposit TX max fee,
- Deposit transaction max fee must be greater than zero.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| _depositDustThreshold | uint64 |  |
| _depositTreasuryFeeDivisor | uint64 | New value of the treasury fee divisor. It is the divisor used to compute the treasury fee taken from each deposit and transferred to the treasury upon sweep proof submission. That fee is computed as follows: `treasuryFee = depositedAmount / depositTreasuryFeeDivisor` For example, if the treasury fee needs to be 2% of each deposit, the `depositTreasuryFeeDivisor` should be set to `50` because `1/50 = 0.02 = 2%`. |
| _depositTxMaxFee | uint64 | New value of the deposit tx max fee in satoshis. It is the maximum amount of BTC transaction fee that can be incurred by each swept deposit being part of the given sweep transaction. If the maximum BTC transaction fee is exceeded, such transaction is considered a fraud. |
| _depositRevealAheadPeriod | uint32 | New value of the deposit reveal ahead period parameter in seconds. It defines the length of the period that must be preserved between the deposit reveal time and the deposit refund locktime. |

### updateRedemptionParameters

```solidity
function updateRedemptionParameters(struct BridgeState.Storage self, uint64 _redemptionDustThreshold, uint64 _redemptionTreasuryFeeDivisor, uint64 _redemptionTxMaxFee, uint64 _redemptionTxMaxTotalFee, uint32 _redemptionTimeout, uint96 _redemptionTimeoutSlashingAmount, uint32 _redemptionTimeoutNotifierRewardMultiplier) internal
```

Updates parameters of redemptions.

Requirements:
- Redemption dust threshold must be greater than moving funds dust
threshold,
- Redemption dust threshold must be greater than the redemption TX
max fee,
- Redemption transaction max fee must be greater than zero,
- Redemption transaction max total fee must be greater than or
equal to the redemption transaction per-request max fee,
- Redemption timeout must be greater than zero,
- Redemption timeout notifier reward multiplier must be in the
range [0, 100].

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| _redemptionDustThreshold | uint64 | New value of the redemption dust threshold in satoshis. It is the minimal amount that can be requested for redemption. Value of this parameter must take into account the value of `redemptionTreasuryFeeDivisor` and `redemptionTxMaxFee` parameters in order to make requests that can incur the treasury and transaction fee and still satisfy the redeemer. |
| _redemptionTreasuryFeeDivisor | uint64 | New value of the redemption treasury fee divisor. It is the divisor used to compute the treasury fee taken from each redemption request and transferred to the treasury upon successful request finalization. That fee is computed as follows: `treasuryFee = requestedAmount / redemptionTreasuryFeeDivisor` For example, if the treasury fee needs to be 2% of each redemption request, the `redemptionTreasuryFeeDivisor` should be set to `50` because `1/50 = 0.02 = 2%`. |
| _redemptionTxMaxFee | uint64 | New value of the redemption transaction max fee in satoshis. It is the maximum amount of BTC transaction fee that can be incurred by each redemption request being part of the given redemption transaction. If the maximum BTC transaction fee is exceeded, such transaction is considered a fraud. This is a per-redemption output max fee for the redemption transaction. |
| _redemptionTxMaxTotalFee | uint64 | New value of the redemption transaction max total fee in satoshis. It is the maximum amount of the total BTC transaction fee that is acceptable in a single redemption transaction. This is a _total_ max fee for the entire redemption transaction. |
| _redemptionTimeout | uint32 | New value of the redemption timeout in seconds. It is the time after which the redemption request can be reported as timed out. It is counted from the moment when the redemption request was created via `requestRedemption` call. Reported  timed out requests are cancelled and locked TBTC is returned to the redeemer in full amount. |
| _redemptionTimeoutSlashingAmount | uint96 | New value of the redemption timeout slashing amount in T, it is the amount slashed from each wallet member for redemption timeout. |
| _redemptionTimeoutNotifierRewardMultiplier | uint32 | New value of the redemption timeout notifier reward multiplier as percentage, it determines the percentage of the notifier reward from the staking contact the notifier of a redemption timeout receives. The value must be in the range [0, 100]. |

### updateMovingFundsParameters

```solidity
function updateMovingFundsParameters(struct BridgeState.Storage self, uint64 _movingFundsTxMaxTotalFee, uint64 _movingFundsDustThreshold, uint32 _movingFundsTimeoutResetDelay, uint32 _movingFundsTimeout, uint96 _movingFundsTimeoutSlashingAmount, uint32 _movingFundsTimeoutNotifierRewardMultiplier, uint16 _movingFundsCommitmentGasOffset, uint64 _movedFundsSweepTxMaxTotalFee, uint32 _movedFundsSweepTimeout, uint96 _movedFundsSweepTimeoutSlashingAmount, uint32 _movedFundsSweepTimeoutNotifierRewardMultiplier) internal
```

Updates parameters of moving funds.

Requirements:
- Moving funds transaction max total fee must be greater than zero,
- Moving funds dust threshold must be greater than zero and lower
than the redemption dust threshold,
- Moving funds timeout reset delay must be greater than zero,
- Moving funds timeout must be greater than the moving funds
timeout reset delay,
- Moving funds timeout notifier reward multiplier must be in the
range [0, 100],
- Moved funds sweep transaction max total fee must be greater than zero,
- Moved funds sweep timeout must be greater than zero,
- Moved funds sweep timeout notifier reward multiplier must be in the
range [0, 100].

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| _movingFundsTxMaxTotalFee | uint64 | New value of the moving funds transaction max total fee in satoshis. It is the maximum amount of the total BTC transaction fee that is acceptable in a single moving funds transaction. This is a _total_ max fee for the entire moving funds transaction. |
| _movingFundsDustThreshold | uint64 | New value of the moving funds dust threshold. It is the minimal satoshi amount that makes sense to be transferred during the moving funds process. Moving funds wallets having their BTC balance below that value can begin closing immediately as transferring such a low value may not be possible due to BTC network fees. |
| _movingFundsTimeoutResetDelay | uint32 | New value of the moving funds timeout reset delay in seconds. It is the time after which the moving funds timeout can be reset in case the target wallet commitment cannot be submitted due to a lack of live wallets in the system. It is counted from the moment when the wallet was requested to move their funds and switched to the MovingFunds state or from the moment the timeout was reset the last time. |
| _movingFundsTimeout | uint32 | New value of the moving funds timeout in seconds. It is the time after which the moving funds process can be reported as timed out. It is counted from the moment when the wallet was requested to move their funds and switched to the MovingFunds state. |
| _movingFundsTimeoutSlashingAmount | uint96 | New value of the moving funds timeout slashing amount in T, it is the amount slashed from each wallet member for moving funds timeout. |
| _movingFundsTimeoutNotifierRewardMultiplier | uint32 | New value of the moving funds timeout notifier reward multiplier as percentage, it determines the percentage of the notifier reward from the staking contact the notifier of a moving funds timeout receives. The value must be in the range [0, 100]. |
| _movingFundsCommitmentGasOffset | uint16 | New value of the gas offset for moving funds target wallet commitment transaction gas costs reimbursement. |
| _movedFundsSweepTxMaxTotalFee | uint64 | New value of the moved funds sweep transaction max total fee in satoshis. It is the maximum amount of the total BTC transaction fee that is acceptable in a single moved funds sweep transaction. This is a _total_ max fee for the entire moved funds sweep transaction. |
| _movedFundsSweepTimeout | uint32 | New value of the moved funds sweep timeout in seconds. It is the time after which the moved funds sweep process can be reported as timed out. It is counted from the moment when the wallet was requested to sweep the received funds. |
| _movedFundsSweepTimeoutSlashingAmount | uint96 | New value of the moved funds sweep timeout slashing amount in T, it is the amount slashed from each wallet member for moved funds sweep timeout. |
| _movedFundsSweepTimeoutNotifierRewardMultiplier | uint32 | New value of the moved funds sweep timeout notifier reward multiplier as percentage, it determines the percentage of the notifier reward from the staking contact the notifier of a moved funds sweep timeout receives. The value must be in the range [0, 100]. |

### updateWalletParameters

```solidity
function updateWalletParameters(struct BridgeState.Storage self, uint32 _walletCreationPeriod, uint64 _walletCreationMinBtcBalance, uint64 _walletCreationMaxBtcBalance, uint64 _walletClosureMinBtcBalance, uint32 _walletMaxAge, uint64 _walletMaxBtcTransfer, uint32 _walletClosingPeriod) internal
```

Requirements:
- Wallet maximum BTC balance must be greater than the wallet
minimum BTC balance,
- Wallet maximum BTC transfer must be greater than zero,
- Wallet closing period must be greater than zero.

### updateFraudParameters

```solidity
function updateFraudParameters(struct BridgeState.Storage self, uint96 _fraudChallengeDepositAmount, uint32 _fraudChallengeDefeatTimeout, uint96 _fraudSlashingAmount, uint32 _fraudNotifierRewardMultiplier) internal
```

Updates parameters related to frauds.

Requirements:
- Fraud challenge defeat timeout must be greater than 0,
- Fraud notifier reward multiplier must be in the range [0, 100].

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| _fraudChallengeDepositAmount | uint96 | New value of the fraud challenge deposit amount in wei, it is the amount of ETH the party challenging the wallet for fraud needs to deposit. |
| _fraudChallengeDefeatTimeout | uint32 | New value of the challenge defeat timeout in seconds, it is the amount of time the wallet has to defeat a fraud challenge. The value must be greater than zero. |
| _fraudSlashingAmount | uint96 | New value of the fraud slashing amount in T, it is the amount slashed from each wallet member for committing a fraud. |
| _fraudNotifierRewardMultiplier | uint32 | New value of the fraud notifier reward multiplier as percentage, it determines the percentage of the notifier reward from the staking contact the notifier of a fraud receives. The value must be in the range [0, 100]. |

### updateTreasury

```solidity
function updateTreasury(struct BridgeState.Storage self, address _treasury) internal
```

Updates treasury address. The treasury receives the system fees.

The treasury address must not be 0x0.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| _treasury | address | New value of the treasury address. |

