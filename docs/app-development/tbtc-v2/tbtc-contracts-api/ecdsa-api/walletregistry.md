# WalletRegistry

## WalletRegistry

### authorization

```solidity
struct EcdsaAuthorization.Data authorization
```

### dkg

```solidity
struct EcdsaDkg.Data dkg
```

### wallets

```solidity
struct Wallets.Data wallets
```

### \_maliciousDkgResultSlashingAmount

```solidity
uint96 _maliciousDkgResultSlashingAmount
```

Slashing amount for submitting a malicious DKG result. Every DKG result submitted can be challenged for the time of `dkg.resultChallengePeriodLength`. If the DKG result submitted is challenged and proven to be malicious, the operator who submitted the malicious result is slashed for `_maliciousDkgResultSlashingAmount`.

### \_maliciousDkgResultNotificationRewardMultiplier

```solidity
uint256 _maliciousDkgResultNotificationRewardMultiplier
```

Percentage of the staking contract malicious behavior notification reward which will be transferred to the notifier reporting about a malicious DKG result. Notifiers are rewarded from a notifiers treasury pool. For example, if notification reward is 1000 and the value of the multiplier is 5, the notifier will receive: 5% of 1000 = 50 per each operator affected.

### \_sortitionPoolRewardsBanDuration

```solidity
uint256 _sortitionPoolRewardsBanDuration
```

Duration of the sortition pool rewards ban imposed on operators who missed their turn for DKG result submission or who failed a heartbeat.

### \_dkgResultSubmissionGas

```solidity
uint256 _dkgResultSubmissionGas
```

Calculated max gas cost for submitting a DKG result. This will be refunded as part of the DKG approval process. It is in the submitter's interest to not skip his priority turn on the approval, otherwise the refund of the DKG submission will be refunded to another group member that will call the DKG approve function.

### \_dkgResultApprovalGasOffset

```solidity
uint256 _dkgResultApprovalGasOffset
```

Gas that is meant to balance the DKG result approval's overall cost. It can be updated by the governance based on the current market conditions.

### \_notifyOperatorInactivityGasOffset

```solidity
uint256 _notifyOperatorInactivityGasOffset
```

Gas that is meant to balance the notification of an operator inactivity. It can be updated by the governance based on the current market conditions.

### \_notifySeedTimeoutGasOffset

```solidity
uint256 _notifySeedTimeoutGasOffset
```

Gas that is meant to balance the notification of a seed for DKG delivery timeout. It can be updated by the governance based on the current market conditions.

### \_notifyDkgTimeoutNegativeGasOffset

```solidity
uint256 _notifyDkgTimeoutNegativeGasOffset
```

Gas that is meant to balance the notification of a DKG protocol execution timeout. It can be updated by the governance based on the current market conditions.

The value is subtracted for the refundable gas calculation, as the DKG timeout notification transaction recovers some gas when cleaning up the storage.

### inactivityClaimNonce

```solidity
mapping(bytes32 => uint256) inactivityClaimNonce
```

Stores current operator inactivity claim nonce for the given wallet signing group. Each claim is made with a unique nonce which protects against claim replay.

### walletOwner

```solidity
contract IWalletOwner walletOwner
```

### sortitionPool

```solidity
contract SortitionPool sortitionPool
```

### staking

```solidity
contract IStaking staking
```

### randomBeacon

```solidity
contract IRandomBeacon randomBeacon
```

### DkgStarted

```solidity
event DkgStarted(uint256 seed)
```

### DkgResultSubmitted

```solidity
event DkgResultSubmitted(bytes32 resultHash, uint256 seed, struct EcdsaDkg.Result result)
```

### DkgTimedOut

```solidity
event DkgTimedOut()
```

### DkgResultApproved

```solidity
event DkgResultApproved(bytes32 resultHash, address approver)
```

### DkgResultChallenged

```solidity
event DkgResultChallenged(bytes32 resultHash, address challenger, string reason)
```

### DkgStateLocked

```solidity
event DkgStateLocked()
```

### DkgSeedTimedOut

```solidity
event DkgSeedTimedOut()
```

### WalletCreated

```solidity
event WalletCreated(bytes32 walletID, bytes32 dkgResultHash)
```

### WalletClosed

```solidity
event WalletClosed(bytes32 walletID)
```

### DkgMaliciousResultSlashed

```solidity
event DkgMaliciousResultSlashed(bytes32 resultHash, uint256 slashingAmount, address maliciousSubmitter)
```

### DkgMaliciousResultSlashingFailed

```solidity
event DkgMaliciousResultSlashingFailed(bytes32 resultHash, uint256 slashingAmount, address maliciousSubmitter)
```

### AuthorizationParametersUpdated

```solidity
event AuthorizationParametersUpdated(uint96 minimumAuthorization, uint64 authorizationDecreaseDelay, uint64 authorizationDecreaseChangePeriod)
```

### RewardParametersUpdated

```solidity
event RewardParametersUpdated(uint256 maliciousDkgResultNotificationRewardMultiplier, uint256 sortitionPoolRewardsBanDuration)
```

### SlashingParametersUpdated

```solidity
event SlashingParametersUpdated(uint256 maliciousDkgResultSlashingAmount)
```

### DkgParametersUpdated

```solidity
event DkgParametersUpdated(uint256 seedTimeout, uint256 resultChallengePeriodLength, uint256 resultChallengeExtraGas, uint256 resultSubmissionTimeout, uint256 resultSubmitterPrecedencePeriodLength)
```

### GasParametersUpdated

```solidity
event GasParametersUpdated(uint256 dkgResultSubmissionGas, uint256 dkgResultApprovalGasOffset, uint256 notifyOperatorInactivityGasOffset, uint256 notifySeedTimeoutGasOffset, uint256 notifyDkgTimeoutNegativeGasOffset)
```

### RandomBeaconUpgraded

```solidity
event RandomBeaconUpgraded(address randomBeacon)
```

### WalletOwnerUpdated

```solidity
event WalletOwnerUpdated(address walletOwner)
```

### OperatorRegistered

```solidity
event OperatorRegistered(address stakingProvider, address operator)
```

### AuthorizationIncreased

```solidity
event AuthorizationIncreased(address stakingProvider, address operator, uint96 fromAmount, uint96 toAmount)
```

### AuthorizationDecreaseRequested

```solidity
event AuthorizationDecreaseRequested(address stakingProvider, address operator, uint96 fromAmount, uint96 toAmount, uint64 decreasingAt)
```

### AuthorizationDecreaseApproved

```solidity
event AuthorizationDecreaseApproved(address stakingProvider)
```

### InvoluntaryAuthorizationDecreaseFailed

```solidity
event InvoluntaryAuthorizationDecreaseFailed(address stakingProvider, address operator, uint96 fromAmount, uint96 toAmount)
```

### OperatorJoinedSortitionPool

```solidity
event OperatorJoinedSortitionPool(address stakingProvider, address operator)
```

### OperatorStatusUpdated

```solidity
event OperatorStatusUpdated(address stakingProvider, address operator)
```

### InactivityClaimed

```solidity
event InactivityClaimed(bytes32 walletID, uint256 nonce, address notifier)
```

### onlyStakingContract

```solidity
modifier onlyStakingContract()
```

### onlyWalletOwner

```solidity
modifier onlyWalletOwner()
```

Reverts if called not by the Wallet Owner.

### onlyReimbursableAdmin

```solidity
modifier onlyReimbursableAdmin()
```

### constructor

```solidity
constructor(contract SortitionPool _sortitionPool, contract IStaking _staking) public
```

Used to initialize immutable variables only, use `initialize` function for upgradable contract initialization on deployment.

### initialize

```solidity
function initialize(contract EcdsaDkgValidator _ecdsaDkgValidator, contract IRandomBeacon _randomBeacon, contract ReimbursementPool _reimbursementPool) external
```

Initializes upgradable contract on deployment.

### withdrawRewards

```solidity
function withdrawRewards(address stakingProvider) external
```

Withdraws application rewards for the given staking provider. Rewards are withdrawn to the staking provider's beneficiary address set in the staking contract. Reverts if staking provider has not registered the operator address.

Emits `RewardsWithdrawn` event.

### withdrawIneligibleRewards

```solidity
function withdrawIneligibleRewards(address recipient) external
```

Withdraws rewards belonging to operators marked as ineligible for sortition pool rewards.

Can be called only by the contract guvnor, which should be the wallet registry governance contract.

#### Parameters

| Name      | Type    | Description                     |
| --------- | ------- | ------------------------------- |
| recipient | address | Recipient of withdrawn rewards. |

### registerOperator

```solidity
function registerOperator(address operator) external
```

Used by staking provider to set operator address that will operate ECDSA node. The given staking provider can set operator address only one time. The operator address can not be changed and must be unique. Reverts if the operator is already set for the staking provider or if the operator address is already in use. Reverts if there is a pending authorization decrease for the staking provider.

### joinSortitionPool

```solidity
function joinSortitionPool() external
```

Lets the operator join the sortition pool. The operator address must be known - before calling this function, it has to be appointed by the staking provider by calling `registerOperator`. Also, the operator must have the minimum authorization required by ECDSA. Function reverts if there is no minimum stake authorized or if the operator is not known. If there was an authorization decrease requested, it is activated by starting the authorization decrease delay.

### updateOperatorStatus

```solidity
function updateOperatorStatus(address operator) external
```

Updates status of the operator in the sortition pool. If there was an authorization decrease requested, it is activated by starting the authorization decrease delay. Function reverts if the operator is not known.

### authorizationIncreased

```solidity
function authorizationIncreased(address stakingProvider, uint96 fromAmount, uint96 toAmount) external
```

Used by T staking contract to inform the application that the authorized stake amount for the given staking provider increased.

Reverts if the authorization amount is below the minimum.

The function is not updating the sortition pool. Sortition pool state needs to be updated by the operator with a call to `joinSortitionPool` or `updateOperatorStatus`.

Can only be called by T staking contract.

### authorizationDecreaseRequested

```solidity
function authorizationDecreaseRequested(address stakingProvider, uint96 fromAmount, uint96 toAmount) external
```

Used by T staking contract to inform the application that the authorization decrease for the given staking provider has been requested.

Reverts if the amount after deauthorization would be non-zero and lower than the minimum authorization.

If the operator is not known (`registerOperator` was not called) it lets to `approveAuthorizationDecrease` immediatelly. If the operator is known (`registerOperator` was called), the operator needs to update state of the sortition pool with a call to `joinSortitionPool` or `updateOperatorStatus`. After the sortition pool state is in sync, authorization decrease delay starts.

After authorization decrease delay passes, authorization decrease request needs to be approved with a call to `approveAuthorizationDecrease` function.

If there is a pending authorization decrease request, it is overwritten.

Can only be called by T staking contract.

### approveAuthorizationDecrease

```solidity
function approveAuthorizationDecrease(address stakingProvider) external
```

Approves the previously registered authorization decrease request. Reverts if authorization decrease delay has not passed yet or if the authorization decrease was not requested for the given staking provider.

### involuntaryAuthorizationDecrease

```solidity
function involuntaryAuthorizationDecrease(address stakingProvider, uint96 fromAmount, uint96 toAmount) external
```

Used by T staking contract to inform the application the authorization has been decreased for the given staking provider involuntarily, as a result of slashing.

If the operator is not known (`registerOperator` was not called) the function does nothing. The operator was never in a sortition pool so there is nothing to update.

If the operator is known, sortition pool is unlocked, and the operator is in the sortition pool, the sortition pool state is updated. If the sortition pool is locked, update needs to be postponed. Every other staker is incentivized to call `updateOperatorStatus` for the problematic operator to increase their own rewards in the pool.

### upgradeRandomBeacon

```solidity
function upgradeRandomBeacon(contract IRandomBeacon _randomBeacon) external
```

Updates address of the Random Beacon.

Can be called only by the contract guvnor, which should be the wallet registry governance contract. The caller is responsible for validating parameters.

#### Parameters

| Name           | Type                   | Description            |
| -------------- | ---------------------- | ---------------------- |
| \_randomBeacon | contract IRandomBeacon | Random Beacon address. |

### updateWalletOwner

```solidity
function updateWalletOwner(contract IWalletOwner _walletOwner) external
```

Updates the wallet owner.

Can be called only by the contract guvnor, which should be the wallet registry governance contract. The caller is responsible for validating parameters. The wallet owner has to implement `IWalletOwner` interface.

#### Parameters

| Name          | Type                  | Description               |
| ------------- | --------------------- | ------------------------- |
| \_walletOwner | contract IWalletOwner | New wallet owner address. |

### updateAuthorizationParameters

```solidity
function updateAuthorizationParameters(uint96 _minimumAuthorization, uint64 _authorizationDecreaseDelay, uint64 _authorizationDecreaseChangePeriod) external
```

Updates the values of authorization parameters.

Can be called only by the contract guvnor, which should be the wallet registry governance contract. The caller is responsible for validating parameters.

#### Parameters

| Name                                | Type   | Description                                          |
| ----------------------------------- | ------ | ---------------------------------------------------- |
| \_minimumAuthorization              | uint96 | New minimum authorization amount.                    |
| \_authorizationDecreaseDelay        | uint64 | New authorization decrease delay in seconds.         |
| \_authorizationDecreaseChangePeriod | uint64 | New authorization decrease change period in seconds. |

### updateDkgParameters

```solidity
function updateDkgParameters(uint256 _seedTimeout, uint256 _resultChallengePeriodLength, uint256 _resultChallengeExtraGas, uint256 _resultSubmissionTimeout, uint256 _submitterPrecedencePeriodLength) external
```

Updates the values of DKG parameters.

Can be called only by the contract guvnor, which should be the wallet registry governance contract. The caller is responsible for validating parameters.

#### Parameters

| Name                              | Type    | Description                                                                                 |
| --------------------------------- | ------- | ------------------------------------------------------------------------------------------- |
| \_seedTimeout                     | uint256 | New seed timeout.                                                                           |
| \_resultChallengePeriodLength     | uint256 | New DKG result challenge period length.                                                     |
| \_resultChallengeExtraGas         | uint256 | New extra gas value required to be left at the end of the DKG result challenge transaction. |
| \_resultSubmissionTimeout         | uint256 | New DKG result submission timeout.                                                          |
| \_submitterPrecedencePeriodLength | uint256 | New submitter precedence period length.                                                     |

### updateRewardParameters

```solidity
function updateRewardParameters(uint256 maliciousDkgResultNotificationRewardMultiplier, uint256 sortitionPoolRewardsBanDuration) external
```

Updates the values of reward parameters.

Can be called only by the contract guvnor, which should be the wallet registry governance contract. The caller is responsible for validating parameters.

#### Parameters

| Name                                           | Type    | Description                                                           |
| ---------------------------------------------- | ------- | --------------------------------------------------------------------- |
| maliciousDkgResultNotificationRewardMultiplier | uint256 | New value of the DKG malicious result notification reward multiplier. |
| sortitionPoolRewardsBanDuration                | uint256 | New sortition pool rewards ban duration in seconds.                   |

### updateSlashingParameters

```solidity
function updateSlashingParameters(uint96 maliciousDkgResultSlashingAmount) external
```

Updates the values of slashing parameters.

Can be called only by the contract guvnor, which should be the wallet registry governance contract. The caller is responsible for validating parameters.

#### Parameters

| Name                             | Type   | Description                               |
| -------------------------------- | ------ | ----------------------------------------- |
| maliciousDkgResultSlashingAmount | uint96 | New malicious DKG result slashing amount. |

### updateGasParameters

```solidity
function updateGasParameters(uint256 dkgResultSubmissionGas, uint256 dkgResultApprovalGasOffset, uint256 notifyOperatorInactivityGasOffset, uint256 notifySeedTimeoutGasOffset, uint256 notifyDkgTimeoutNegativeGasOffset) external
```

Updates the values of gas-related parameters.

Can be called only by the contract guvnor, which should be the wallet registry governance contract. The caller is responsible for validating parameters.

#### Parameters

| Name                              | Type    | Description                                                |
| --------------------------------- | ------- | ---------------------------------------------------------- |
| dkgResultSubmissionGas            | uint256 | New DKG result submission gas.                             |
| dkgResultApprovalGasOffset        | uint256 | New DKG result approval gas offset.                        |
| notifyOperatorInactivityGasOffset | uint256 | New operator inactivity notification gas offset.           |
| notifySeedTimeoutGasOffset        | uint256 | New seed for DKG delivery timeout notification gas offset. |
| notifyDkgTimeoutNegativeGasOffset | uint256 | New DKG timeout notification gas offset.                   |

### requestNewWallet

```solidity
function requestNewWallet() external
```

Requests a new wallet creation.

Can be called only by the owner of wallets. It locks the DKG and request a new relay entry. It expects that the DKG process will be started once a new relay entry gets generated.

### closeWallet

```solidity
function closeWallet(bytes32 walletID) external
```

Closes an existing wallet. Reverts if wallet with the given ID does not exist or if it has already been closed.

Only a Wallet Owner can call this function.

#### Parameters

| Name     | Type    | Description       |
| -------- | ------- | ----------------- |
| walletID | bytes32 | ID of the wallet. |

### \_\_beaconCallback

```solidity
function __beaconCallback(uint256 relayEntry, uint256) external
```

A callback that is executed once a new relay entry gets generated. It starts the DKG process.

Can be called only by the random beacon contract.

#### Parameters

| Name       | Type    | Description  |
| ---------- | ------- | ------------ |
| relayEntry | uint256 | Relay entry. |
|            | uint256 |              |

### submitDkgResult

```solidity
function submitDkgResult(struct EcdsaDkg.Result dkgResult) external
```

Submits result of DKG protocol. The DKG result consists of result submitting member index, calculated group public key, bytes array of misbehaved members, concatenation of signatures from group members, indices of members corresponding to each signature and the list of group members. The result is registered optimistically and waits for an approval. The result can be challenged when it is believed to be incorrect. The challenge verifies the registered result i.a. it checks if members list corresponds to the expected set of members determined by the sortition pool.

The message to be signed by each member is keccak256 hash of the chain ID, calculated group public key, misbehaved members indices and DKG start block. The calculated hash should be prefixed with `\x19Ethereum signed message:` before signing, so the message to sign is: `\x19Ethereum signed message:\n${keccak256(chainID,groupPubKey,misbehavedIndices,startBlock)}`

#### Parameters

| Name      | Type                   | Description |
| --------- | ---------------------- | ----------- |
| dkgResult | struct EcdsaDkg.Result | DKG result. |

### approveDkgResult

```solidity
function approveDkgResult(struct EcdsaDkg.Result dkgResult) external
```

Approves DKG result. Can be called when the challenge period for the submitted result is finished. Considers the submitted result as valid, bans misbehaved group members from the sortition pool rewards, and completes the group creation by activating the candidate group. For the first `resultSubmissionTimeout` blocks after the end of the challenge period can be called only by the DKG result submitter. After that time, can be called by anyone. A new wallet based on the DKG result details.

#### Parameters

| Name      | Type                   | Description                                                                         |
| --------- | ---------------------- | ----------------------------------------------------------------------------------- |
| dkgResult | struct EcdsaDkg.Result | Result to approve. Must match the submitted result stored during `submitDkgResult`. |

### notifySeedTimeout

```solidity
function notifySeedTimeout() external
```

Notifies about seed for DKG delivery timeout. It is expected that a seed is delivered by the Random Beacon as a relay entry in a callback function.

### notifyDkgTimeout

```solidity
function notifyDkgTimeout() external
```

Notifies about DKG timeout.

### challengeDkgResult

```solidity
function challengeDkgResult(struct EcdsaDkg.Result dkgResult) external
```

Challenges DKG result. If the submitted result is proved to be invalid it reverts the DKG back to the result submission phase.

Due to EIP-150 1/64 of the gas is not forwarded to the call, and will be kept to execute the remaining operations in the function after the call inside the try-catch. To eliminate a class of attacks related to the gas limit manipulation, this function requires an extra amount of gas to be left at the end of the execution.

#### Parameters

| Name      | Type                   | Description                                                                           |
| --------- | ---------------------- | ------------------------------------------------------------------------------------- |
| dkgResult | struct EcdsaDkg.Result | Result to challenge. Must match the submitted result stored during `submitDkgResult`. |

### notifyOperatorInactivity

```solidity
function notifyOperatorInactivity(struct EcdsaInactivity.Claim claim, uint256 nonce, uint32[] groupMembers) external
```

Notifies about operators who are inactive. Using this function, a majority of the wallet signing group can decide about punishing specific group members who constantly fail doing their job. If the provided claim is proved to be valid and signed by sufficient number of group members, operators of members deemed as inactive are banned from sortition pool rewards for the duration specified by `sortitionPoolRewardsBanDuration` parameter. The function allows to signal about single operators being inactive as well as to signal wallet-wide heartbeat failures that are propagated to the wallet owner who should begin the procedure of moving responsibilities to another wallet given that the wallet who failed the heartbeat may soon be not able to function and provide new signatures. The sender of the claim must be one of the claim signers. This function can be called only for registered wallets

#### Parameters

| Name         | Type                         | Description                                                                                            |
| ------------ | ---------------------------- | ------------------------------------------------------------------------------------------------------ |
| claim        | struct EcdsaInactivity.Claim | Operator inactivity claim.                                                                             |
| nonce        | uint256                      | Current inactivity claim nonce for the given wallet signing group. Must be the same as the stored one. |
| groupMembers | uint32\[]                    | Identifiers of the wallet signing group members.                                                       |

### seize

```solidity
function seize(uint96 amount, uint256 rewardMultiplier, address notifier, bytes32 walletID, uint32[] walletMembersIDs) external
```

Allows the wallet owner to add all signing group members of the wallet with the given ID to the slashing queue of the staking . contract. The notifier will receive reward per each group member from the staking contract notifiers treasury. The reward is scaled by the `rewardMultiplier` provided as a parameter.

Requirements:

* The expression `keccak256(abi.encode(walletMembersIDs))` must be exactly the same as the hash stored under `membersIdsHash` for the given `walletID`. Those IDs are not directly stored in the contract for gas efficiency purposes but they can be read from appropriate `DkgResultSubmitted` and `DkgResultApproved` events.
* `rewardMultiplier` must be between \[0, 100].
* This function does revert if staking contract call reverts. The calling code needs to handle the potential revert.

#### Parameters

| Name             | Type      | Description                                                                                                 |
| ---------------- | --------- | ----------------------------------------------------------------------------------------------------------- |
| amount           | uint96    | Amount of tokens to seize from each signing group member.                                                   |
| rewardMultiplier | uint256   | Fraction of the staking contract notifiers reward the notifier should receive; should be between \[0, 100]. |
| notifier         | address   | Address of the misbehavior notifier.                                                                        |
| walletID         | bytes32   | ID of the wallet.                                                                                           |
| walletMembersIDs | uint32\[] | Identifiers of the wallet signing group members.                                                            |

### isDkgResultValid

```solidity
function isDkgResultValid(struct EcdsaDkg.Result result) external view returns (bool, string)
```

Checks if DKG result is valid for the current DKG.

#### Parameters

| Name   | Type                   | Description |
| ------ | ---------------------- | ----------- |
| result | struct EcdsaDkg.Result | DKG result. |

#### Return Values

| Name | Type   | Description                                                                                  |
| ---- | ------ | -------------------------------------------------------------------------------------------- |
| \[0] | bool   | True if the result is valid. If the result is invalid it returns false and an error message. |
| \[1] | string |                                                                                              |

### getWalletCreationState

```solidity
function getWalletCreationState() external view returns (enum EcdsaDkg.State)
```

Check current wallet creation state.

### isWalletMember

```solidity
function isWalletMember(bytes32 walletID, uint32[] walletMembersIDs, address operator, uint256 walletMemberIndex) external view returns (bool)
```

Checks whether the given operator is a member of the given wallet signing group.

Requirements:

* The `operator` parameter must be an actual sortition pool operator.
* The expression `keccak256(abi.encode(walletMembersIDs))` must be exactly the same as the hash stored under `membersIdsHash` for the given `walletID`. Those IDs are not directly stored in the contract for gas efficiency purposes but they can be read from appropriate `DkgResultSubmitted` and `DkgResultApproved` events.
* The `walletMemberIndex` must be in range \[1, walletMembersIDs.length]

#### Parameters

| Name              | Type      | Description                                                        |
| ----------------- | --------- | ------------------------------------------------------------------ |
| walletID          | bytes32   | ID of the wallet.                                                  |
| walletMembersIDs  | uint32\[] | Identifiers of the wallet signing group members.                   |
| operator          | address   | Address of the checked operator.                                   |
| walletMemberIndex | uint256   | Position of the operator in the wallet signing group members list. |

#### Return Values

| Name | Type | Description                                                                              |
| ---- | ---- | ---------------------------------------------------------------------------------------- |
| \[0] | bool | True - if the operator is a member of the given wallet signing group. False - otherwise. |

### hasSeedTimedOut

```solidity
function hasSeedTimedOut() external view returns (bool)
```

Checks if awaiting seed timed out.

#### Return Values

| Name | Type | Description                                       |
| ---- | ---- | ------------------------------------------------- |
| \[0] | bool | True if awaiting seed timed out, false otherwise. |

### hasDkgTimedOut

```solidity
function hasDkgTimedOut() external view returns (bool)
```

Checks if DKG timed out. The DKG timeout period includes time required for off-chain protocol execution and time for the result publication for all group members. After this time result cannot be submitted and DKG can be notified about the timeout.

#### Return Values

| Name | Type | Description                             |
| ---- | ---- | --------------------------------------- |
| \[0] | bool | True if DKG timed out, false otherwise. |

### getWallet

```solidity
function getWallet(bytes32 walletID) external view returns (struct Wallets.Wallet)
```

### getWalletPublicKey

```solidity
function getWalletPublicKey(bytes32 walletID) external view returns (bytes)
```

Gets public key of a wallet with a given wallet ID. The public key is returned in an uncompressed format as a 64-byte concatenation of X and Y coordinates.

#### Parameters

| Name     | Type    | Description       |
| -------- | ------- | ----------------- |
| walletID | bytes32 | ID of the wallet. |

#### Return Values

| Name | Type  | Description                            |
| ---- | ----- | -------------------------------------- |
| \[0] | bytes | Uncompressed public key of the wallet. |

### isWalletRegistered

```solidity
function isWalletRegistered(bytes32 walletID) external view returns (bool)
```

Checks if a wallet with the given ID is registered.

#### Parameters

| Name     | Type    | Description  |
| -------- | ------- | ------------ |
| walletID | bytes32 | Wallet's ID. |

#### Return Values

| Name | Type | Description                                    |
| ---- | ---- | ---------------------------------------------- |
| \[0] | bool | True if wallet is registered, false otherwise. |

### minimumAuthorization

```solidity
function minimumAuthorization() external view returns (uint96)
```

The minimum authorization amount required so that operator can participate in ECDSA Wallet operations.

### eligibleStake

```solidity
function eligibleStake(address stakingProvider) external view returns (uint96)
```

Returns the current value of the staking provider's eligible stake. Eligible stake is defined as the currently authorized stake minus the pending authorization decrease. Eligible stake is what is used for operator's weight in the sortition pool. If the authorized stake minus the pending authorization decrease is below the minimum authorization, eligible stake is 0.

### availableRewards

```solidity
function availableRewards(address stakingProvider) external view returns (uint96)
```

Returns the amount of rewards available for withdrawal for the given staking provider. Reverts if staking provider has not registered the operator address.

### pendingAuthorizationDecrease

```solidity
function pendingAuthorizationDecrease(address stakingProvider) external view returns (uint96)
```

Returns the amount of stake that is pending authorization decrease for the given staking provider. If no authorization decrease has been requested, returns zero.

### remainingAuthorizationDecreaseDelay

```solidity
function remainingAuthorizationDecreaseDelay(address stakingProvider) external view returns (uint64)
```

Returns the remaining time in seconds that needs to pass before the requested authorization decrease can be approved. If the sortition pool state was not updated yet by the operator after requesting the authorization decrease, returns `type(uint64).max`.

### stakingProviderToOperator

```solidity
function stakingProviderToOperator(address stakingProvider) public view returns (address)
```

Returns operator registered for the given staking provider.

### operatorToStakingProvider

```solidity
function operatorToStakingProvider(address operator) public view returns (address)
```

Returns staking provider of the given operator.

### isOperatorUpToDate

```solidity
function isOperatorUpToDate(address operator) external view returns (bool)
```

Checks if the operator's authorized stake is in sync with operator's weight in the sortition pool. If the operator is not in the sortition pool and their authorized stake is non-zero, function returns false.

### isOperatorInPool

```solidity
function isOperatorInPool(address operator) external view returns (bool)
```

Returns true if the given operator is in the sortition pool. Otherwise, returns false.

### selectGroup

```solidity
function selectGroup() external view returns (uint32[])
```

Selects a new group of operators. Can only be called when DKG is in progress and the pool is locked. At least one operator has to be registered in the pool, otherwise the function fails reverting the transaction.

#### Return Values

| Name | Type      | Description                    |
| ---- | --------- | ------------------------------ |
| \[0] | uint32\[] | IDs of selected group members. |

### dkgParameters

```solidity
function dkgParameters() external view returns (struct EcdsaDkg.Parameters)
```

Retrieves dkg parameters that were set in DKG library.

### authorizationParameters

```solidity
function authorizationParameters() external view returns (uint96 minimumAuthorization, uint64 authorizationDecreaseDelay, uint64 authorizationDecreaseChangePeriod)
```

Returns authorization-related parameters.

The minimum authorization is also returned by `minimumAuthorization()` function, as a requirement of `IApplication` interface.

#### Return Values

| Name                              | Type   | Description                                                                                                                                                                                                                                                                                                                                                                                                 |
| --------------------------------- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| minimumAuthorization              | uint96 | The minimum authorization amount required so that operator can participate in the random beacon. This amount is required to execute slashing for providing a malicious DKG result or when a relay entry times out.                                                                                                                                                                                          |
| authorizationDecreaseDelay        | uint64 | Delay in seconds that needs to pass between the time authorization decrease is requested and the time that request gets approved. Protects against free-riders earning rewards and not being active in the network.                                                                                                                                                                                         |
| authorizationDecreaseChangePeriod | uint64 | Authorization decrease change period in seconds. It is the time, before authorization decrease delay end, during which the pending authorization decrease request can be overwritten. If set to 0, pending authorization decrease request can not be overwritten until the entire `authorizationDecreaseDelay` ends. If set to value equal `authorizationDecreaseDelay`, request can always be overwritten. |

### rewardParameters

```solidity
function rewardParameters() external view returns (uint256 maliciousDkgResultNotificationRewardMultiplier, uint256 sortitionPoolRewardsBanDuration)
```

Retrieves reward-related parameters.

#### Return Values

| Name                                           | Type    | Description                                                                                                                                                                                                                                                                                                                                                                 |
| ---------------------------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| maliciousDkgResultNotificationRewardMultiplier | uint256 | Percentage of the staking contract malicious behavior notification reward which will be transferred to the notifier reporting about a malicious DKG result. Notifiers are rewarded from a notifiers treasury pool. For example, if notification reward is 1000 and the value of the multiplier is 5, the notifier will receive: 5% of 1000 = 50 per each operator affected. |
| sortitionPoolRewardsBanDuration                | uint256 | Duration of the sortition pool rewards ban imposed on operators who missed their turn for DKG result submission or who failed a heartbeat.                                                                                                                                                                                                                                  |

### slashingParameters

```solidity
function slashingParameters() external view returns (uint96 maliciousDkgResultSlashingAmount)
```

Retrieves slashing-related parameters.

#### Return Values

| Name                             | Type   | Description                                                                                                                                                                                                                                                                                                                      |
| -------------------------------- | ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| maliciousDkgResultSlashingAmount | uint96 | Slashing amount for submitting a malicious DKG result. Every DKG result submitted can be challenged for the time of `dkg.resultChallengePeriodLength`. If the DKG result submitted is challenged and proven to be malicious, the operator who submitted the malicious result is slashed for `_maliciousDkgResultSlashingAmount`. |

### gasParameters

```solidity
function gasParameters() external view returns (uint256 dkgResultSubmissionGas, uint256 dkgResultApprovalGasOffset, uint256 notifyOperatorInactivityGasOffset, uint256 notifySeedTimeoutGasOffset, uint256 notifyDkgTimeoutNegativeGasOffset)
```

Retrieves gas-related parameters.

#### Return Values

| Name                              | Type    | Description                                                                                                                                                                                                                                                                                                                   |
| --------------------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| dkgResultSubmissionGas            | uint256 | Calculated max gas cost for submitting a DKG result. This will be refunded as part of the DKG approval process. It is in the submitter's interest to not skip his priority turn on the approval, otherwise the refund of the DKG submission will be refunded to another group member that will call the DKG approve function. |
| dkgResultApprovalGasOffset        | uint256 | Gas that is meant to balance the DKG result approval's overall cost. It can be updated by the governance based on the current market conditions.                                                                                                                                                                              |
| notifyOperatorInactivityGasOffset | uint256 | Gas that is meant to balance the notification of an operator inactivity. It can be updated by the governance based on the current market conditions.                                                                                                                                                                          |
| notifySeedTimeoutGasOffset        | uint256 | Gas that is meant to balance the notification of a seed for DKG delivery timeout. It can be updated by the governance based on the current market conditions.                                                                                                                                                                 |
| notifyDkgTimeoutNegativeGasOffset | uint256 | Gas that is meant to balance the notification of a DKG protocol execution timeout. It can be updated by the governance based on the current market conditions.                                                                                                                                                                |
