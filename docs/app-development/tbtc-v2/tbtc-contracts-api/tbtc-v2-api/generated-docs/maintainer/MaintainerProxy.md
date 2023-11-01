# Solidity API

## MaintainerProxy

Maintainers are the willing off-chain clients approved by the governance.
Maintainers proxy calls to the `Bridge` contract via 'MaintainerProxy'
and are refunded for the spent gas from the `ReimbursementPool`.
There are two types of maintainers: wallet maintainers and SPV
maintainers.

### bridge

```solidity
contract Bridge bridge
```

### isWalletMaintainer

```solidity
mapping(address => uint256) isWalletMaintainer
```

Authorized wallet maintainers that can interact with the set of
functions for wallet maintainers only. Authorization can be
granted and removed by the governance.

'Key' is the address of the maintainer. 'Value' represents an index+1
in the 'maintainers' array. 1 was added so the maintainer index can
never be 0 which is a reserved index for a non-existent maintainer
in this map.

### walletMaintainers

```solidity
address[] walletMaintainers
```

This list of wallet maintainers keeps the order of which wallet
maintainer should be submitting a next transaction. It does not
enforce the order but only tracks who should be next in line.

### isSpvMaintainer

```solidity
mapping(address => uint256) isSpvMaintainer
```

Authorized SPV maintainers that can interact with the set of
functions for SPV maintainers only. Authorization can be
granted and removed by the governance.

'Key' is the address of the maintainer. 'Value' represents an index+1
in the 'maintainers' array. 1 was added so the maintainer index can
never be 0 which is a reserved index for a non-existent maintainer
in this map.

### spvMaintainers

```solidity
address[] spvMaintainers
```

This list of SPV maintainers keeps the order of which SPV
maintainer should be submitting a next transaction. It does not
enforce the order but only tracks who should be next in line.

### submitDepositSweepProofGasOffset

```solidity
uint256 submitDepositSweepProofGasOffset
```

Gas that is meant to balance the submission of deposit sweep proof
overall cost. Can be updated by the governance based on the current
market conditions.

### submitRedemptionProofGasOffset

```solidity
uint256 submitRedemptionProofGasOffset
```

Gas that is meant to balance the submission of redemption proof
overall cost. Can be updated by the governance based on the current
market conditions.

### resetMovingFundsTimeoutGasOffset

```solidity
uint256 resetMovingFundsTimeoutGasOffset
```

Gas that is meant to balance the reset of moving funds timeout
overall cost. Can be updated by the governance based on the current
market conditions.

### submitMovingFundsProofGasOffset

```solidity
uint256 submitMovingFundsProofGasOffset
```

Gas that is meant to balance the submission of moving funds proof
overall cost. Can be updated by the governance based on the current
market conditions.

### notifyMovingFundsBelowDustGasOffset

```solidity
uint256 notifyMovingFundsBelowDustGasOffset
```

Gas that is meant to balance the notification of moving funds below
dust overall cost. Can be updated by the governance based on the
current market conditions.

### submitMovedFundsSweepProofGasOffset

```solidity
uint256 submitMovedFundsSweepProofGasOffset
```

Gas that is meant to balance the submission of moved funds sweep
proof overall cost. Can be updated by the governance based on the
current market conditions.

### requestNewWalletGasOffset

```solidity
uint256 requestNewWalletGasOffset
```

Gas that is meant to balance the request of a new wallet overall
cost. Can be updated by the governance based on the current
market conditions.

### notifyWalletCloseableGasOffset

```solidity
uint256 notifyWalletCloseableGasOffset
```

Gas that is meant to balance the notification of closeable wallet
overall cost. Can be updated by the governance based on the current
market conditions.

### notifyWalletClosingPeriodElapsedGasOffset

```solidity
uint256 notifyWalletClosingPeriodElapsedGasOffset
```

Gas that is meant to balance the notification of wallet closing
period elapsed overall cost. Can be updated by the governance
based on the current market conditions.

### defeatFraudChallengeGasOffset

```solidity
uint256 defeatFraudChallengeGasOffset
```

Gas that is meant to balance the defeat fraud challenge
overall cost. Can be updated by the governance based on the current
market conditions.

### defeatFraudChallengeWithHeartbeatGasOffset

```solidity
uint256 defeatFraudChallengeWithHeartbeatGasOffset
```

Gas that is meant to balance the defeat fraud challenge with heartbeat
overall cost. Can be updated by the governance based on the current
market conditions.

### WalletMaintainerAuthorized

```solidity
event WalletMaintainerAuthorized(address maintainer)
```

### WalletMaintainerUnauthorized

```solidity
event WalletMaintainerUnauthorized(address maintainer)
```

### SpvMaintainerAuthorized

```solidity
event SpvMaintainerAuthorized(address maintainer)
```

### SpvMaintainerUnauthorized

```solidity
event SpvMaintainerUnauthorized(address maintainer)
```

### BridgeUpdated

```solidity
event BridgeUpdated(address newBridge)
```

### GasOffsetParametersUpdated

```solidity
event GasOffsetParametersUpdated(uint256 submitDepositSweepProofGasOffset, uint256 submitRedemptionProofGasOffset, uint256 resetMovingFundsTimeoutGasOffset, uint256 submitMovingFundsProofGasOffset, uint256 notifyMovingFundsBelowDustGasOffset, uint256 submitMovedFundsSweepProofGasOffset, uint256 requestNewWalletGasOffset, uint256 notifyWalletCloseableGasOffset, uint256 notifyWalletClosingPeriodElapsedGasOffset, uint256 defeatFraudChallengeGasOffset, uint256 defeatFraudChallengeWithHeartbeatGasOffset)
```

### onlyWalletMaintainer

```solidity
modifier onlyWalletMaintainer()
```

### onlySpvMaintainer

```solidity
modifier onlySpvMaintainer()
```

### onlyReimbursableAdmin

```solidity
modifier onlyReimbursableAdmin()
```

### constructor

```solidity
constructor(contract Bridge _bridge, contract ReimbursementPool _reimbursementPool) public
```

### submitDepositSweepProof

```solidity
function submitDepositSweepProof(struct BitcoinTx.Info sweepTx, struct BitcoinTx.Proof sweepProof, struct BitcoinTx.UTXO mainUtxo, address vault) external
```

Wraps `Bridge.submitDepositSweepProof` call and reimburses the
caller's transaction cost.

See `Bridge.submitDepositSweepProof` function documentation.

### submitRedemptionProof

```solidity
function submitRedemptionProof(struct BitcoinTx.Info redemptionTx, struct BitcoinTx.Proof redemptionProof, struct BitcoinTx.UTXO mainUtxo, bytes20 walletPubKeyHash) external
```

Wraps `Bridge.submitRedemptionProof` call and reimburses the
caller's transaction cost.

See `Bridge.submitRedemptionProof` function documentation.

### resetMovingFundsTimeout

```solidity
function resetMovingFundsTimeout(bytes20 walletPubKeyHash) external
```

Wraps `Bridge.resetMovingFundsTimeout` call and reimburses the
caller's transaction cost.

See `Bridge.resetMovingFundsTimeout` function documentation.

### submitMovingFundsProof

```solidity
function submitMovingFundsProof(struct BitcoinTx.Info movingFundsTx, struct BitcoinTx.Proof movingFundsProof, struct BitcoinTx.UTXO mainUtxo, bytes20 walletPubKeyHash) external
```

Wraps `Bridge.submitMovingFundsProof` call and reimburses the
caller's transaction cost.

See `Bridge.submitMovingFundsProof` function documentation.

### notifyMovingFundsBelowDust

```solidity
function notifyMovingFundsBelowDust(bytes20 walletPubKeyHash, struct BitcoinTx.UTXO mainUtxo) external
```

Wraps `Bridge.notifyMovingFundsBelowDust` call and reimburses the
caller's transaction cost.

See `Bridge.notifyMovingFundsBelowDust` function documentation.

### submitMovedFundsSweepProof

```solidity
function submitMovedFundsSweepProof(struct BitcoinTx.Info sweepTx, struct BitcoinTx.Proof sweepProof, struct BitcoinTx.UTXO mainUtxo) external
```

Wraps `Bridge.submitMovedFundsSweepProof` call and reimburses the
caller's transaction cost.

See `Bridge.submitMovedFundsSweepProof` function documentation.

### requestNewWallet

```solidity
function requestNewWallet(struct BitcoinTx.UTXO activeWalletMainUtxo) external
```

Wraps `Bridge.requestNewWallet` call and reimburses the
caller's transaction cost.

See `Bridge.requestNewWallet` function documentation.

### notifyWalletCloseable

```solidity
function notifyWalletCloseable(bytes20 walletPubKeyHash, struct BitcoinTx.UTXO walletMainUtxo) external
```

Wraps `Bridge.notifyWalletCloseable` call and reimburses the
caller's transaction cost.

See `Bridge.notifyWalletCloseable` function documentation.

### notifyWalletClosingPeriodElapsed

```solidity
function notifyWalletClosingPeriodElapsed(bytes20 walletPubKeyHash) external
```

Wraps `Bridge.notifyWalletClosingPeriodElapsed` call and reimburses
the caller's transaction cost.

See `Bridge.notifyWalletClosingPeriodElapsed` function documentation.

### defeatFraudChallenge

```solidity
function defeatFraudChallenge(bytes walletPublicKey, bytes preimage, bool witness) external
```

Wraps `Bridge.defeatFraudChallenge` call and reimburses the
caller's transaction cost.

See `Bridge.defeatFraudChallenge` function documentation.

### defeatFraudChallengeWithHeartbeat

```solidity
function defeatFraudChallengeWithHeartbeat(bytes walletPublicKey, bytes heartbeatMessage) external
```

Wraps `Bridge.defeatFraudChallengeWithHeartbeat` call and
reimburses the caller's transaction cost.

See `Bridge.defeatFraudChallengeWithHeartbeat` function documentation.

### authorizeWalletMaintainer

```solidity
function authorizeWalletMaintainer(address maintainer) external
```

Authorize a wallet maintainer that can interact with this
reimbursement pool. Can be authorized by the owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| maintainer | address | Wallet maintainer to authorize. |

### authorizeSpvMaintainer

```solidity
function authorizeSpvMaintainer(address maintainer) external
```

Authorize an SPV maintainer that can interact with this
reimbursement pool. Can be authorized by the owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| maintainer | address | SPV maintainer to authorize. |

### unauthorizeWalletMaintainer

```solidity
function unauthorizeWalletMaintainer(address maintainerToUnauthorize) external
```

Unauthorize a wallet maintainer that was previously authorized to
interact with the Maintainer Proxy contract. Can be unauthorized
by the owner only.

The last maintainer is swapped with the one to be unauthorized.
The unauthorized maintainer is then removed from the list. An index
of the last maintainer is changed with the removed maintainer.
Ex.
'walletMaintainers' list: [0x1, 0x2, 0x3, 0x4, 0x5]
'isWalletMaintainer' map: [0x1 -> 1, 0x2 -> 2, 0x3 -> 3, 0x4 -> 4, 0x5 -> 5]
unauthorize: 0x3
new 'walletMaintainers' list: [0x1, 0x2, 0x5, 0x4]
new 'isWalletMaintainer' map: [0x1 -> 1, 0x2 -> 2, 0x4 -> 4, 0x5 -> 3]

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| maintainerToUnauthorize | address | Maintainer to unauthorize. |

### unauthorizeSpvMaintainer

```solidity
function unauthorizeSpvMaintainer(address maintainerToUnauthorize) external
```

Unauthorize an SPV maintainer that was previously authorized to
interact with the Maintainer Proxy contract. Can be unauthorized
by the owner only.

The last maintainer is swapped with the one to be unauthorized.
The unauthorized maintainer is then removed from the list. An index
of the last maintainer is changed with the removed maintainer.
Ex.
'spvMaintainers' list: [0x1, 0x2, 0x3, 0x4, 0x5]
'isSpvMaintainer' map: [0x1 -> 1, 0x2 -> 2, 0x3 -> 3, 0x4 -> 4, 0x5 -> 5]
unauthorize: 0x3
new 'spvMaintainers' list: [0x1, 0x2, 0x5, 0x4]
new 'isSpvMaintainer' map: [0x1 -> 1, 0x2 -> 2, 0x4 -> 4, 0x5 -> 3]

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| maintainerToUnauthorize | address | Maintainer to unauthorize. |

### updateBridge

```solidity
function updateBridge(contract Bridge _bridge) external
```

Allows the Governance to upgrade the Bridge address.

The function does not implement any governance delay and does not
check the status of the Bridge. The Governance implementation needs
to ensure all requirements for the upgrade are satisfied before
executing this function.

### updateGasOffsetParameters

```solidity
function updateGasOffsetParameters(uint256 newSubmitDepositSweepProofGasOffset, uint256 newSubmitRedemptionProofGasOffset, uint256 newResetMovingFundsTimeoutGasOffset, uint256 newSubmitMovingFundsProofGasOffset, uint256 newNotifyMovingFundsBelowDustGasOffset, uint256 newSubmitMovedFundsSweepProofGasOffset, uint256 newRequestNewWalletGasOffset, uint256 newNotifyWalletCloseableGasOffset, uint256 newNotifyWalletClosingPeriodElapsedGasOffset, uint256 newDefeatFraudChallengeGasOffset, uint256 newDefeatFraudChallengeWithHeartbeatGasOffset) external
```

Updates the values of gas offset parameters.

Can be called only by the contract owner. The caller is responsible
for validating parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| newSubmitDepositSweepProofGasOffset | uint256 | New submit deposit sweep proof gas offset. |
| newSubmitRedemptionProofGasOffset | uint256 | New submit redemption proof gas offset. |
| newResetMovingFundsTimeoutGasOffset | uint256 | New reset moving funds timeout gas offset. |
| newSubmitMovingFundsProofGasOffset | uint256 | New submit moving funds proof gas offset. |
| newNotifyMovingFundsBelowDustGasOffset | uint256 | New notify moving funds below dust gas offset. |
| newSubmitMovedFundsSweepProofGasOffset | uint256 | New submit moved funds sweep proof gas offset. |
| newRequestNewWalletGasOffset | uint256 | New request new wallet gas offset. |
| newNotifyWalletCloseableGasOffset | uint256 | New notify closeable wallet gas offset. |
| newNotifyWalletClosingPeriodElapsedGasOffset | uint256 | New notify wallet closing period elapsed gas offset. |
| newDefeatFraudChallengeGasOffset | uint256 | New defeat fraud challenge gas offset. |
| newDefeatFraudChallengeWithHeartbeatGasOffset | uint256 | New defeat fraud challenge with heartbeat gas offset. |

### allWalletMaintainers

```solidity
function allWalletMaintainers() external view returns (address[])
```

Gets an entire array of wallet maintainer addresses.

### allSpvMaintainers

```solidity
function allSpvMaintainers() external view returns (address[])
```

Gets an entire array of SPV maintainer addresses.

