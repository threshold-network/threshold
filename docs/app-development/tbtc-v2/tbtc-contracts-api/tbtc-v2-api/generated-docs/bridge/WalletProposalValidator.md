# Solidity API

## WalletProposalValidator

This contract exposes several view functions allowing to validate
specific wallet action proposals. This contract is non-upgradeable
and does not have any write functions.

### DepositSweepProposal

```solidity
struct DepositSweepProposal {
  bytes20 walletPubKeyHash;
  struct WalletProposalValidator.DepositKey[] depositsKeys;
  uint256 sweepTxFee;
  uint256[] depositsRevealBlocks;
}
```

### DepositKey

```solidity
struct DepositKey {
  bytes32 fundingTxHash;
  uint32 fundingOutputIndex;
}
```

### DepositExtraInfo

```solidity
struct DepositExtraInfo {
  struct BitcoinTx.Info fundingTx;
  bytes8 blindingFactor;
  bytes20 walletPubKeyHash;
  bytes20 refundPubKeyHash;
  bytes4 refundLocktime;
}
```

### RedemptionProposal

```solidity
struct RedemptionProposal {
  bytes20 walletPubKeyHash;
  bytes[] redeemersOutputScripts;
  uint256 redemptionTxFee;
}
```

### MovingFundsProposal

```solidity
struct MovingFundsProposal {
  bytes20 walletPubKeyHash;
  bytes20[] targetWallets;
  uint256 movingFundsTxFee;
}
```

### MovedFundsSweepProposal

```solidity
struct MovedFundsSweepProposal {
  bytes20 walletPubKeyHash;
  bytes32 movingFundsTxHash;
  uint32 movingFundsTxOutputIndex;
  uint256 movedFundsSweepTxFee;
}
```

### HeartbeatProposal

```solidity
struct HeartbeatProposal {
  bytes20 walletPubKeyHash;
  bytes message;
}
```

### bridge

```solidity
contract Bridge bridge
```

Handle to the Bridge contract.

### DEPOSIT_MIN_AGE

```solidity
uint32 DEPOSIT_MIN_AGE
```

The minimum time that must elapse since the deposit reveal
before a deposit becomes eligible for a deposit sweep.

For example, if a deposit was revealed at 9 am and DEPOSIT_MIN_AGE
is 2 hours, the deposit is eligible for sweep after 11 am.

Forcing deposit minimum age ensures block finality for Ethereum.
In the happy path case, i.e. where the deposit is revealed immediately
after being broadcast on the Bitcoin network, the minimum age
check also ensures block finality for Bitcoin.

### DEPOSIT_REFUND_SAFETY_MARGIN

```solidity
uint32 DEPOSIT_REFUND_SAFETY_MARGIN
```

Each deposit can be technically swept until it reaches its
refund timestamp after which it can be taken back by the depositor.
However, allowing the wallet to sweep deposits that are close
to their refund timestamp may cause a race between the wallet
and the depositor. In result, the wallet may sign an invalid
sweep transaction that aims to sweep an already refunded deposit.
Such tx signature may be used to create an undefeatable fraud
challenge against the wallet. In order to mitigate that problem,
this parameter determines a safety margin that puts the latest
moment a deposit can be swept far before the point after which
the deposit becomes refundable.

For example, if a deposit becomes refundable after 8 pm and
DEPOSIT_REFUND_SAFETY_MARGIN is 6 hours, the deposit is valid
for a sweep only before 2 pm.

### DEPOSIT_SWEEP_MAX_SIZE

```solidity
uint16 DEPOSIT_SWEEP_MAX_SIZE
```

The maximum count of deposits that can be swept within a
single sweep.

### REDEMPTION_REQUEST_MIN_AGE

```solidity
uint32 REDEMPTION_REQUEST_MIN_AGE
```

The minimum time that must elapse since the redemption request
creation before a request becomes eligible for a processing.

For example, if a request was created at 9 am and
REDEMPTION_REQUEST_MIN_AGE is 2 hours, the request is
eligible for processing after 11 am.

Forcing request minimum age ensures block finality for Ethereum.

### REDEMPTION_REQUEST_TIMEOUT_SAFETY_MARGIN

```solidity
uint32 REDEMPTION_REQUEST_TIMEOUT_SAFETY_MARGIN
```

Each redemption request can be technically handled until it
reaches its timeout timestamp after which it can be reported
as timed out. However, allowing the wallet to handle requests
that are close to their timeout timestamp may cause a race
between the wallet and the redeemer. In result, the wallet may
redeem the requested funds even though the redeemer already
received back their tBTC (locked during redemption request) upon
reporting the request timeout. In effect, the redeemer may end
out with both tBTC and redeemed BTC in their hands which has
a negative impact on the tBTC <-> BTC peg. In order to mitigate
that problem, this parameter determines a safety margin that
puts the latest moment a request can be handled far before the
point after which the request can be reported as timed out.

For example, if a request times out after 8 pm and
REDEMPTION_REQUEST_TIMEOUT_SAFETY_MARGIN is 2 hours, the
request is valid for processing only before 6 pm.

### REDEMPTION_MAX_SIZE

```solidity
uint16 REDEMPTION_MAX_SIZE
```

The maximum count of redemption requests that can be processed
within a single redemption.

### constructor

```solidity
constructor(contract Bridge _bridge) public
```

### validateDepositSweepProposal

```solidity
function validateDepositSweepProposal(struct WalletProposalValidator.DepositSweepProposal proposal, struct WalletProposalValidator.DepositExtraInfo[] depositsExtraInfo) external view returns (bool)
```

View function encapsulating the main rules of a valid deposit
sweep proposal. This function is meant to facilitate the off-chain
validation of the incoming proposals. Thanks to it, most
of the work can be done using a single readonly contract call.
Worth noting, the validation done here is not exhaustive as some
conditions may not be verifiable within the on-chain function or
checking them may be easier on the off-chain side. For example,
this function does not check the SPV proofs and confirmations of
the deposit funding transactions as this would require an
integration with the difficulty relay that greatly increases
complexity. Instead of that, each off-chain wallet member is
supposed to do that check on their own.

Requirements:
- The target wallet must be in the Live or MovingFunds state,
- The number of deposits included in the sweep must be in
the range [1, `DEPOSIT_SWEEP_MAX_SIZE`],
- The length of `depositsExtraInfo` array must be equal to the
length of `proposal.depositsKeys`, i.e. each deposit must
have exactly one set of corresponding extra info,
- The proposed sweep tx fee must be grater than zero,
- The proposed maximum per-deposit sweep tx fee must be lesser than
or equal the maximum fee allowed by the Bridge (`Bridge.depositTxMaxFee`),
- Each deposit must be revealed to the Bridge,
- Each deposit must be old enough, i.e. at least `DEPOSIT_MIN_AGE
elapsed since their reveal time,
- Each deposit must not be swept yet,
- Each deposit must have valid extra info (see `validateDepositExtraInfo`),
- Each deposit must have the refund safety margin preserved,
- Each deposit must be controlled by the same wallet,
- Each deposit must target the same vault,
- Each deposit must be unique.

The following off-chain validation must be performed as a bare minimum:
- Inputs used for the sweep transaction have enough Bitcoin confirmations,
- Deposits revealed to the Bridge have enough Ethereum confirmations.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| proposal | struct WalletProposalValidator.DepositSweepProposal | The sweeping proposal to validate. |
| depositsExtraInfo | struct WalletProposalValidator.DepositExtraInfo[] | Deposits extra info required to perform the validation. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if the proposal is valid. Reverts otherwise. |

### validateSweepTxFee

```solidity
function validateSweepTxFee(uint256 sweepTxFee, uint256 depositsCount) internal view
```

Validates the sweep tx fee by checking if the part of the fee
incurred by each deposit does not exceed the maximum value
allowed by the Bridge. This function is heavily based on
`DepositSweep.depositSweepTxFeeDistribution` function.

Requirements:
- The sweep tx fee must be grater than zero,
- The maximum per-deposit sweep tx fee must be lesser than or equal
the maximum fee allowed by the Bridge (`Bridge.depositTxMaxFee`).

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| sweepTxFee | uint256 | The sweep transaction fee. |
| depositsCount | uint256 | Count of the deposits swept by the sweep transaction. |

### validateDepositExtraInfo

```solidity
function validateDepositExtraInfo(struct WalletProposalValidator.DepositKey depositKey, address depositor, bytes32 extraData, struct WalletProposalValidator.DepositExtraInfo depositExtraInfo) internal view
```

Validates the extra info for the given deposit. This function
is heavily based on `Deposit.revealDeposit` function.

Requirements:
- The transaction hash computed using `depositExtraInfo.fundingTx`
must match the `depositKey.fundingTxHash`. This requirement
ensures the funding transaction data provided in the extra
data container actually represent the funding transaction of
the given deposit.
- The P2(W)SH script inferred from `depositExtraInfo` is actually
used to lock funds by the `depositKey.fundingOutputIndex` output
of the `depositExtraInfo.fundingTx` transaction. This requirement
ensures the reveal data provided in the extra info container
actually matches the given deposit.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| depositKey | struct WalletProposalValidator.DepositKey | Key of the given deposit. |
| depositor | address | Depositor that revealed the deposit. |
| extraData | bytes32 | 32-byte deposit extra data. Optional, can be bytes32(0). |
| depositExtraInfo | struct WalletProposalValidator.DepositExtraInfo | Extra info being subject of the validation. |

### validateRedemptionProposal

```solidity
function validateRedemptionProposal(struct WalletProposalValidator.RedemptionProposal proposal) external view returns (bool)
```

View function encapsulating the main rules of a valid redemption
proposal. This function is meant to facilitate the off-chain
validation of the incoming proposals. Thanks to it, most
of the work can be done using a single readonly contract call.

Requirements:
- The target wallet must be in the Live or MovingFunds state,
- The number of redemption requests included in the redemption
proposal must be in the range [1, `redemptionMaxSize`],
- The proposed redemption tx fee must be grater than zero,
- The proposed redemption tx fee must be lesser than or equal to
the maximum total fee allowed by the Bridge
(`Bridge.redemptionTxMaxTotalFee`),
- The proposed maximum per-request redemption tx fee share must be
lesser than or equal to the maximum fee share allowed by the
given request (`RedemptionRequest.txMaxFee`),
- Each request must be a pending request registered in the Bridge,
- Each request must be old enough, i.e. at least `REDEMPTION_REQUEST_MIN_AGE`
OR the delay enforced by the redemption watchtower
(if the watchtower is set and the returned delay is greater than `REDEMPTION_REQUEST_MIN_AGE`)
elapsed since their creation time,
- Each request must have the timeout safety margin preserved,
- Each request must be unique.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| proposal | struct WalletProposalValidator.RedemptionProposal | The redemption proposal to validate. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if the proposal is valid. Reverts otherwise. |

### validateMovingFundsProposal

```solidity
function validateMovingFundsProposal(struct WalletProposalValidator.MovingFundsProposal proposal, struct BitcoinTx.UTXO walletMainUtxo) external view returns (bool)
```

View function encapsulating the main rules of a valid moving
funds proposal. This function is meant to facilitate the
off-chain validation of the incoming proposals. Thanks to it,
most of the work can be done using a single readonly contract
call.

Notice that this function is meant to be invoked after the moving
funds commitment has already been submitted. This function skips
some checks related to the moving funds procedure as they were
already checked on the commitment submission.
Requirements:
- The source wallet must be in the MovingFunds state,
- The target wallets commitment must be submitted,
- The target wallets commitment hash must match the target wallets
from the proposal,
- The source wallet BTC balance must be equal to or greater than
`movingFundsDustThreshold`,
- The proposed moving funds transaction fee must be greater than
zero,
- The proposed moving funds transaction fee must not exceed the
maximum total fee allowed for moving funds.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| proposal | struct WalletProposalValidator.MovingFundsProposal | The moving funds proposal to validate. |
| walletMainUtxo | struct BitcoinTx.UTXO | The main UTXO of the source wallet. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if the proposal is valid. Reverts otherwise. |

### getWalletBtcBalance

```solidity
function getWalletBtcBalance(bytes32 walletMainUtxoHash, struct BitcoinTx.UTXO walletMainUtxo) internal view returns (uint64 walletBtcBalance)
```

Calculates the Bitcoin balance of a wallet based on its main
UTXO.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletMainUtxoHash | bytes32 | The hash of the wallet's main UTXO. |
| walletMainUtxo | struct BitcoinTx.UTXO | The detailed data of the wallet's main UTXO. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletBtcBalance | uint64 | The calculated Bitcoin balance of the wallet. |

### validateMovedFundsSweepProposal

```solidity
function validateMovedFundsSweepProposal(struct WalletProposalValidator.MovedFundsSweepProposal proposal) external view returns (bool)
```

View function encapsulating the main rules of a valid moved
funds sweep proposal. This function is meant to facilitate the
off-chain validation of the incoming proposals. Thanks to it,
most of the work can be done using a single readonly contract
call.

Requirements:
- The source wallet must be in the Live or MovingFunds state,
- The moved funds sweep request identified by the proposed
transaction hash and output index must be in the Pending state,
- The transaction hash and output index from the proposal must
identify a moved funds sweep request in the Pending state,
- The transaction hash and output index from the proposal must
identify a moved funds sweep request that belongs to the wallet,
- The proposed moved funds sweep transaction fee must be greater
than zero,
- The proposed moved funds sweep transaction fee must not exceed
the maximum total fee allowed for moved funds sweep.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| proposal | struct WalletProposalValidator.MovedFundsSweepProposal | The moved funds sweep proposal to validate. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if the proposal is valid. Reverts otherwise. |

### validateHeartbeatProposal

```solidity
function validateHeartbeatProposal(struct WalletProposalValidator.HeartbeatProposal proposal) external view returns (bool)
```

View function encapsulating the main rules of a valid heartbeat
proposal. This function is meant to facilitate the off-chain
validation of the incoming proposals. Thanks to it, most
of the work can be done using a single readonly contract call.

Requirements:
- The message to sign is a valid heartbeat message.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| proposal | struct WalletProposalValidator.HeartbeatProposal | The heartbeat proposal to validate. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if the proposal is valid. Reverts otherwise. |

