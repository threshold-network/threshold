# Solidity API

## RandomBeacon

Keep Random Beacon contract. It lets to request a new
relay entry and validates the new relay entry provided by the
network. This contract is in charge of all other Random Beacon
activities such as group lifecycle or slashing.

Should be owned by the governance contract controlling Random Beacon
parameters.

### genesisSeed

```solidity
uint256 genesisSeed
```

Seed value used for the genesis group selection.
https://www.wolframalpha.com/input/?i=pi+to+78+digits

### _callbackGasLimit

```solidity
uint256 _callbackGasLimit
```

Relay entry callback gas limit. This is the gas limit with which
callback function provided in the relay request transaction is
executed. The callback is executed with a new relay entry value
in the same transaction the relay entry is submitted.

### _groupCreationFrequency

```solidity
uint256 _groupCreationFrequency
```

The frequency of new group creation. Groups are created with
a fixed frequency of relay requests.

### _maliciousDkgResultSlashingAmount

```solidity
uint96 _maliciousDkgResultSlashingAmount
```

Slashing amount for submitting a malicious DKG result. Every
DKG result submitted can be challenged for the time of
`dkg.ResultChallengePeriodLength`. If the DKG result submitted
is challenged and proven to be malicious, the operator who
submitted the malicious result is slashed for
`_maliciousDkgResultSlashingAmount`.

### _unauthorizedSigningSlashingAmount

```solidity
uint96 _unauthorizedSigningSlashingAmount
```

Slashing amount when an unauthorized signing has been proved,
which means the private key leaked and all the group members
should be punished.

### _sortitionPoolRewardsBanDuration

```solidity
uint256 _sortitionPoolRewardsBanDuration
```

Duration of the sortition pool rewards ban imposed on operators
who misbehaved during DKG by being inactive or disqualified and
for operators that were identified by the rest of group members
as inactive via `notifyOperatorInactivity`.

### _relayEntryTimeoutNotificationRewardMultiplier

```solidity
uint256 _relayEntryTimeoutNotificationRewardMultiplier
```

Percentage of the staking contract malicious behavior
notification reward which will be transferred to the notifier
reporting about relay entry timeout. Notifiers are rewarded
from a notifiers treasury pool. For example, if
notification reward is 1000 and the value of the multiplier is
5, the notifier will receive: 5% of 1000 = 50 per each
operator affected.

### _unauthorizedSigningNotificationRewardMultiplier

```solidity
uint256 _unauthorizedSigningNotificationRewardMultiplier
```

Percentage of the staking contract malicious behavior
notification reward which will be transferred to the notifier
reporting about unauthorized signing. Notifiers are rewarded
from a notifiers treasury pool. For example, if a
notification reward is 1000 and the value of the multiplier is
5, the notifier will receive: 5% of 1000 = 50 per each
operator affected.

### _dkgMaliciousResultNotificationRewardMultiplier

```solidity
uint256 _dkgMaliciousResultNotificationRewardMultiplier
```

Percentage of the staking contract malicious behavior
notification reward which will be transferred to the notifier
reporting about a malicious DKG result. Notifiers are rewarded
from a notifiers treasury pool. For example, if
notification reward is 1000 and the value of the multiplier is
5, the notifier will receive: 5% of 1000 = 50 per each
operator affected.

### _dkgResultSubmissionGas

```solidity
uint256 _dkgResultSubmissionGas
```

Calculated gas cost for submitting a DKG result. This will
be refunded as part of the DKG approval process. It is in the
submitter's interest to not skip his priority turn on the approval,
otherwise the refund of the DKG submission will be refunded to
another group member that will call the DKG approve function.

### _dkgResultApprovalGasOffset

```solidity
uint256 _dkgResultApprovalGasOffset
```

Gas that is meant to balance the DKG result approval's overall
cost. Can be updated by the governance based on the current
market conditions.

### _notifyOperatorInactivityGasOffset

```solidity
uint256 _notifyOperatorInactivityGasOffset
```

Gas that is meant to balance the operator inactivity notification
cost. Can be updated by the governance based on the current
market conditions.

### _relayEntrySubmissionGasOffset

```solidity
uint256 _relayEntrySubmissionGasOffset
```

Gas that is meant to balance the relay entry submission cost.
Can be updated by the governance based on the current market
conditions.

### inactivityClaimNonce

```solidity
mapping(uint64 => uint256) inactivityClaimNonce
```

Stores current operator inactivity claim nonce for given group.
Each claim is made with an unique nonce which protects
against claim replay.

### authorizedRequesters

```solidity
mapping(address => bool) authorizedRequesters
```

Authorized addresses that can request a relay entry.

### sortitionPool

```solidity
contract SortitionPool sortitionPool
```

### tToken

```solidity
contract IERC20 tToken
```

### staking

```solidity
contract IStaking staking
```

### authorization

```solidity
struct BeaconAuthorization.Data authorization
```

### dkg

```solidity
struct BeaconDkg.Data dkg
```

### groups

```solidity
struct Groups.Data groups
```

### relay

```solidity
struct Relay.Data relay
```

### callback

```solidity
struct Callback.Data callback
```

### AuthorizationParametersUpdated

```solidity
event AuthorizationParametersUpdated(uint96 minimumAuthorization, uint64 authorizationDecreaseDelay, uint64 authorizationDecreaseChangePeriod)
```

### RelayEntryParametersUpdated

```solidity
event RelayEntryParametersUpdated(uint256 relayEntrySoftTimeout, uint256 relayEntryHardTimeout, uint256 callbackGasLimit)
```

### GroupCreationParametersUpdated

```solidity
event GroupCreationParametersUpdated(uint256 groupCreationFrequency, uint256 groupLifetime, uint256 dkgResultChallengePeriodLength, uint256 dkgResultChallengeExtraGas, uint256 dkgResultSubmissionTimeout, uint256 dkgResultSubmitterPrecedencePeriodLength)
```

### RewardParametersUpdated

```solidity
event RewardParametersUpdated(uint256 sortitionPoolRewardsBanDuration, uint256 relayEntryTimeoutNotificationRewardMultiplier, uint256 unauthorizedSigningNotificationRewardMultiplier, uint256 dkgMaliciousResultNotificationRewardMultiplier)
```

### SlashingParametersUpdated

```solidity
event SlashingParametersUpdated(uint256 relayEntrySubmissionFailureSlashingAmount, uint256 maliciousDkgResultSlashingAmount, uint256 unauthorizedSigningSlashingAmount)
```

### GasParametersUpdated

```solidity
event GasParametersUpdated(uint256 dkgResultSubmissionGas, uint256 dkgResultApprovalGasOffset, uint256 notifyOperatorInactivityGasOffset, uint256 relayEntrySubmissionGasOffset)
```

### RequesterAuthorizationUpdated

```solidity
event RequesterAuthorizationUpdated(address requester, bool isAuthorized)
```

### DkgStarted

```solidity
event DkgStarted(uint256 seed)
```

### DkgResultSubmitted

```solidity
event DkgResultSubmitted(bytes32 resultHash, uint256 seed, struct BeaconDkg.Result result)
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

### DkgMaliciousResultSlashed

```solidity
event DkgMaliciousResultSlashed(bytes32 resultHash, uint256 slashingAmount, address maliciousSubmitter)
```

### DkgMaliciousResultSlashingFailed

```solidity
event DkgMaliciousResultSlashingFailed(bytes32 resultHash, uint256 slashingAmount, address maliciousSubmitter)
```

### DkgStateLocked

```solidity
event DkgStateLocked()
```

### DkgSeedTimedOut

```solidity
event DkgSeedTimedOut()
```

### GroupRegistered

```solidity
event GroupRegistered(uint64 groupId, bytes groupPubKey)
```

### RelayEntryRequested

```solidity
event RelayEntryRequested(uint256 requestId, uint64 groupId, bytes previousEntry)
```

### RelayEntrySubmitted

```solidity
event RelayEntrySubmitted(uint256 requestId, address submitter, bytes entry)
```

### RelayEntryTimedOut

```solidity
event RelayEntryTimedOut(uint256 requestId, uint64 terminatedGroupId)
```

### RelayEntryDelaySlashed

```solidity
event RelayEntryDelaySlashed(uint256 requestId, uint256 slashingAmount, address[] groupMembers)
```

### RelayEntryDelaySlashingFailed

```solidity
event RelayEntryDelaySlashingFailed(uint256 requestId, uint256 slashingAmount, address[] groupMembers)
```

### RelayEntryTimeoutSlashed

```solidity
event RelayEntryTimeoutSlashed(uint256 requestId, uint256 slashingAmount, address[] groupMembers)
```

### RelayEntryTimeoutSlashingFailed

```solidity
event RelayEntryTimeoutSlashingFailed(uint256 requestId, uint256 slashingAmount, address[] groupMembers)
```

### UnauthorizedSigningSlashed

```solidity
event UnauthorizedSigningSlashed(uint64 groupId, uint256 unauthorizedSigningSlashingAmount, address[] groupMembers)
```

### UnauthorizedSigningSlashingFailed

```solidity
event UnauthorizedSigningSlashingFailed(uint64 groupId, uint256 unauthorizedSigningSlashingAmount, address[] groupMembers)
```

### CallbackFailed

```solidity
event CallbackFailed(uint256 entry, uint256 entrySubmittedBlock)
```

### InactivityClaimed

```solidity
event InactivityClaimed(uint64 groupId, uint256 nonce, address notifier)
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

### constructor

```solidity
constructor(contract SortitionPool _sortitionPool, contract IERC20 _tToken, contract IStaking _staking, contract BeaconDkgValidator _dkgValidator, contract ReimbursementPool _reimbursementPool) public
```

Assigns initial values to parameters to make the beacon work
safely. These parameters are just proposed defaults and they might
be updated with `update*` functions after the contract deployment
and before transferring the ownership to the governance contract.

### onlyStakingContract

```solidity
modifier onlyStakingContract()
```

### onlyReimbursableAdmin

```solidity
modifier onlyReimbursableAdmin()
```

### updateAuthorizationParameters

```solidity
function updateAuthorizationParameters(uint96 _minimumAuthorization, uint64 _authorizationDecreaseDelay, uint64 _authorizationDecreaseChangePeriod) external
```

Updates the values of authorization parameters.

Can be called only by the contract guvnor, which should be the
random beacon governance contract. The caller is responsible for
validating parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _minimumAuthorization | uint96 | New minimum authorization amount |
| _authorizationDecreaseDelay | uint64 | New authorization decrease delay in seconds |
| _authorizationDecreaseChangePeriod | uint64 | New authorization decrease change period in seconds |

### updateRelayEntryParameters

```solidity
function updateRelayEntryParameters(uint256 relayEntrySoftTimeout, uint256 relayEntryHardTimeout, uint256 callbackGasLimit) external
```

Updates the values of relay entry parameters.

Can be called only by the contract guvnor, which should be the
random beacon governance contract. The caller is responsible for
validating parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| relayEntrySoftTimeout | uint256 | New relay entry submission soft timeout |
| relayEntryHardTimeout | uint256 | New relay entry hard timeout |
| callbackGasLimit | uint256 | New callback gas limit |

### updateGroupCreationParameters

```solidity
function updateGroupCreationParameters(uint256 groupCreationFrequency, uint256 groupLifetime, uint256 dkgResultChallengePeriodLength, uint256 dkgResultChallengeExtraGas, uint256 dkgResultSubmissionTimeout, uint256 dkgSubmitterPrecedencePeriodLength) external
```

Updates the values of group creation parameters.

Can be called only by the contract guvnor, which should be the
random beacon governance contract. The caller is responsible for
validating parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| groupCreationFrequency | uint256 | New group creation frequency |
| groupLifetime | uint256 | New group lifetime in blocks |
| dkgResultChallengePeriodLength | uint256 | New DKG result challenge period length |
| dkgResultChallengeExtraGas | uint256 | New DKG result challenge extra gas |
| dkgResultSubmissionTimeout | uint256 | New DKG result submission timeout |
| dkgSubmitterPrecedencePeriodLength | uint256 | New DKG result submitter precedence period length |

### updateRewardParameters

```solidity
function updateRewardParameters(uint256 sortitionPoolRewardsBanDuration, uint256 relayEntryTimeoutNotificationRewardMultiplier, uint256 unauthorizedSigningNotificationRewardMultiplier, uint256 dkgMaliciousResultNotificationRewardMultiplier) external
```

Updates the values of reward parameters.

Can be called only by the contract guvnor, which should be the
random beacon governance contract. The caller is responsible for
validating parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| sortitionPoolRewardsBanDuration | uint256 | New sortition pool rewards ban duration in seconds. |
| relayEntryTimeoutNotificationRewardMultiplier | uint256 | New value of the relay entry timeout notification reward multiplier |
| unauthorizedSigningNotificationRewardMultiplier | uint256 | New value of the unauthorized signing notification reward multiplier |
| dkgMaliciousResultNotificationRewardMultiplier | uint256 | New value of the DKG malicious result notification reward multiplier |

### updateSlashingParameters

```solidity
function updateSlashingParameters(uint96 relayEntrySubmissionFailureSlashingAmount, uint96 maliciousDkgResultSlashingAmount, uint96 unauthorizedSigningSlashingAmount) external
```

Updates the values of slashing parameters.

Can be called only by the contract guvnor, which should be the
random beacon governance contract. The caller is responsible for
validating parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| relayEntrySubmissionFailureSlashingAmount | uint96 | New relay entry submission failure amount |
| maliciousDkgResultSlashingAmount | uint96 | New malicious DKG result slashing amount |
| unauthorizedSigningSlashingAmount | uint96 | New unauthorized signing slashing amount |

### updateGasParameters

```solidity
function updateGasParameters(uint256 dkgResultSubmissionGas, uint256 dkgResultApprovalGasOffset, uint256 notifyOperatorInactivityGasOffset, uint256 relayEntrySubmissionGasOffset) external
```

Updates the values of gas parameters.

Can be called only by the contract guvnor, which should be the
random beacon governance contract. The caller is responsible for
validating parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| dkgResultSubmissionGas | uint256 | New DKG result submission gas |
| dkgResultApprovalGasOffset | uint256 | New DKG result approval gas offset |
| notifyOperatorInactivityGasOffset | uint256 | New operator inactivity notification gas offset |
| relayEntrySubmissionGasOffset | uint256 | New relay entry submission gas offset |

### setRequesterAuthorization

```solidity
function setRequesterAuthorization(address requester, bool isAuthorized) external
```

Set authorization for requesters that can request a relay
entry.

Can be called only by the contract guvnor, which should be the
random beacon governance contract.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| requester | address | Requester, can be a contract or EOA |
| isAuthorized | bool | True or false |

### withdrawRewards

```solidity
function withdrawRewards(address stakingProvider) external
```

Withdraws application rewards for the given staking provider.
Rewards are withdrawn to the staking provider's beneficiary
address set in the staking contract. Reverts if staking provider
has not registered the operator address.

Emits `RewardsWithdrawn` event.

### withdrawIneligibleRewards

```solidity
function withdrawIneligibleRewards(address recipient) external
```

Withdraws rewards belonging to operators marked as ineligible
for sortition pool rewards.

Can be called only by the contract guvnor, which should be the
random beacon governance contract.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| recipient | address | Recipient of withdrawn rewards. |

### registerOperator

```solidity
function registerOperator(address operator) external
```

Used by staking provider to set operator address that will
operate a node. The given staking provider can set operator
address only one time. The operator address can not be changed
and must be unique. Reverts if the operator is already set for
the staking provider or if the operator address is already in
use. Reverts if there is a pending authorization decrease for
the staking provider.

### joinSortitionPool

```solidity
function joinSortitionPool() external
```

Lets the operator join the sortition pool. The operator address
must be known - before calling this function, it has to be
appointed by the staking provider by calling `registerOperator`.
Also, the operator must have the minimum authorization required
by the beacon. Function reverts if there is no minimum stake
authorized or if the operator is not known. If there was an
authorization decrease requested, it is activated by starting
the authorization decrease delay.

### updateOperatorStatus

```solidity
function updateOperatorStatus(address operator) external
```

Updates status of the operator in the sortition pool. If there
was an authorization decrease requested, it is activated by
starting the authorization decrease delay.
Function reverts if the operator is not known.

### authorizationIncreased

```solidity
function authorizationIncreased(address stakingProvider, uint96 fromAmount, uint96 toAmount) external
```

Used by T staking contract to inform the beacon that the
authorized stake amount for the given staking provider increased.

Reverts if the authorization amount is below the minimum.

The function is not updating the sortition pool. Sortition pool
state needs to be updated by the operator with a call to
`joinSortitionPool` or `updateOperatorStatus`.

Can only be called by T staking contract.

### authorizationDecreaseRequested

```solidity
function authorizationDecreaseRequested(address stakingProvider, uint96 fromAmount, uint96 toAmount) external
```

Used by T staking contract to inform the beacon that the
authorization decrease for the given staking provider has been
requested.

Reverts if the amount after deauthorization would be non-zero
and lower than the minimum authorization.

Reverts if another authorization decrease request is pending for
the staking provider and not enough time passed since the
original request (see `authorizationDecreaseChangePeriod`).

If the operator is not known (`registerOperator` was not called)
it lets to `approveAuthorizationDecrease` immediately. If the
operator is known (`registerOperator` was called), the operator
needs to update state of the sortition pool with a call to
`joinSortitionPool` or `updateOperatorStatus`. After the
sortition pool state is in sync, authorization decrease delay
starts.

After authorization decrease delay passes, authorization
decrease request needs to be approved with a call to
`approveAuthorizationDecrease` function.

If there is a pending authorization decrease request, it is
overwritten, but only if enough time passed since the original
request. Otherwise, the function reverts.

Can only be called by T staking contract.

### approveAuthorizationDecrease

```solidity
function approveAuthorizationDecrease(address stakingProvider) external
```

Approves the previously registered authorization decrease
request. Reverts if authorization decrease delay has not passed
yet or if the authorization decrease was not requested for the
given staking provider.

### involuntaryAuthorizationDecrease

```solidity
function involuntaryAuthorizationDecrease(address stakingProvider, uint96 fromAmount, uint96 toAmount) external
```

Used by T staking contract to inform the beacon the
authorization has been decreased for the given staking provider
involuntarily, as a result of slashing.

If the operator is not known (`registerOperator` was not called)
the function does nothing. The operator was never in a sortition
pool so there is nothing to update.

If the operator is known, sortition pool is unlocked, and the
operator is in the sortition pool, the sortition pool state is
updated. If the sortition pool is locked, update needs to be
postponed. Every other staker is incentivized to call
`updateOperatorStatus` for the problematic operator to increase
their own rewards in the pool.

### genesis

```solidity
function genesis() external
```

Triggers group selection if there are no active groups.

### submitDkgResult

```solidity
function submitDkgResult(struct BeaconDkg.Result dkgResult) external
```

`\x19Ethereum signed message:\n` before signing, so the message to
sign is:
`\x19Ethereum signed message:\n${keccak256(chainID,groupPubKey,misbehaved,startBlock)}`

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| dkgResult | struct BeaconDkg.Result | DKG result. |

### notifyDkgTimeout

```solidity
function notifyDkgTimeout() external
```

Notifies about DKG timeout.

### approveDkgResult

```solidity
function approveDkgResult(struct BeaconDkg.Result dkgResult) external
```

Approves DKG result. Can be called when the challenge period for
the submitted result is finished. Considers the submitted result
as valid, bans misbehaved group members from the sortition pool
rewards, and completes the group creation by activating the
candidate group. For the first `submitterPrecedencePeriodLength`
blocks after the end of the challenge period can be called only
by the DKG result submitter. After that time, can be called by
anyone.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| dkgResult | struct BeaconDkg.Result | Result to approve. Must match the submitted result stored during `submitDkgResult`. |

### challengeDkgResult

```solidity
function challengeDkgResult(struct BeaconDkg.Result dkgResult) external
```

Challenges DKG result. If the submitted result is proved to be
invalid it reverts the DKG back to the result submission phase.
It removes a candidate group that was previously registered with
the DKG result submission.

Due to EIP-150 1/64 of the gas is not forwarded to the call, and
will be kept to execute the remaining operations in the function
after the call inside the try-catch. To eliminate a class of
attacks related to the gas limit manipulation, this function
requires an extra amount of gas to be left at the end of the
execution.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| dkgResult | struct BeaconDkg.Result | Result to challenge. Must match the submitted result stored during `submitDkgResult`. |

### getGroupCreationState

```solidity
function getGroupCreationState() external view returns (enum BeaconDkg.State)
```

Check current group creation state.

### hasDkgTimedOut

```solidity
function hasDkgTimedOut() external view returns (bool)
```

Checks if DKG timed out. The DKG timeout period includes time required
for off-chain protocol execution and time for the result publication
for all group members. After this time result cannot be submitted
and DKG can be notified about the timeout.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if DKG timed out, false otherwise. |

### getGroupsRegistry

```solidity
function getGroupsRegistry() external view returns (bytes32[])
```

### getGroup

```solidity
function getGroup(uint64 groupId) external view returns (struct Groups.Group)
```

### getGroup

```solidity
function getGroup(bytes groupPubKey) external view returns (struct Groups.Group)
```

### requestRelayEntry

```solidity
function requestRelayEntry(contract IRandomBeaconConsumer callbackContract) external
```

Creates a request to generate a new relay entry, which will
include a random number (by signing the previous entry's
random number). Requester must be previously authorized by the
governance.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| callbackContract | contract IRandomBeaconConsumer | Beacon consumer callback contract. |

### submitRelayEntry

```solidity
function submitRelayEntry(bytes entry) external
```

Creates a new relay entry. Gas-optimized version that can be
called only before the soft timeout. This should be the majority
of cases.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| entry | bytes | Group BLS signature over the previous entry. |

### submitRelayEntry

```solidity
function submitRelayEntry(bytes entry, uint32[] groupMembers) external
```

Creates a new relay entry.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| entry | bytes | Group BLS signature over the previous entry. |
| groupMembers | uint32[] | Identifiers of group members. |

### reportRelayEntryTimeout

```solidity
function reportRelayEntryTimeout(uint32[] groupMembers) external
```

Reports a relay entry timeout.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| groupMembers | uint32[] | Identifiers of group members. |

### reportUnauthorizedSigning

```solidity
function reportUnauthorizedSigning(bytes signedMsgSender, uint64 groupId, uint32[] groupMembers) external
```

Reports unauthorized groups signing. Must provide a valid signature
of the sender's address as a message. Successful signature
verification means the private key has been leaked and all group
members should be punished by slashing their tokens. Group has
to be active or expired. Unauthorized signing cannot be reported
for a terminated group. In case of reporting unauthorized
signing for a terminated group, or when the signature is invalid,
function reverts.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| signedMsgSender | bytes | Signature of the sender's address as a message. |
| groupId | uint64 | Group that is being reported for leaking a private key. |
| groupMembers | uint32[] | Identifiers of group members. |

### notifyOperatorInactivity

```solidity
function notifyOperatorInactivity(struct BeaconInactivity.Claim claim, uint256 nonce, uint32[] groupMembers) external
```

Notifies about operators who are inactive. Using this function,
a majority of the group can decide about punishing specific
group members who constantly fail doing their job. If the provided
claim is proved to be valid and signed by sufficient number
of group members, operators of members deemed as inactive are
banned for sortition pool rewards for duration specified by
`_sortitionPoolRewardsBanDuration` parameter. The sender of
the claim must be one of the claim signers. This function can be
called only for active and non-terminated groups.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| claim | struct BeaconInactivity.Claim | Operator inactivity claim. |
| nonce | uint256 | Current inactivity claim nonce for the given group. Must be the same as the stored one. |
| groupMembers | uint32[] | Identifiers of group members. |

### minimumAuthorization

```solidity
function minimumAuthorization() external view returns (uint96)
```

The minimum authorization amount required so that operator can
participate in the random beacon. This amount is required to
execute slashing for providing a malicious DKG result or when
a relay entry times out.

### isRelayRequestInProgress

```solidity
function isRelayRequestInProgress() external view returns (bool)
```

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | Flag indicating whether a relay entry request is currently in progress. |

### eligibleStake

```solidity
function eligibleStake(address stakingProvider) external view returns (uint96)
```

Returns the current value of the staking provider's eligible
stake. Eligible stake is defined as the currently authorized
stake minus the pending authorization decrease. Eligible stake
is what is used for operator's weight in the sortition pool.
If the authorized stake minus the pending authorization decrease
is below the minimum authorization, eligible stake is 0.

### availableRewards

```solidity
function availableRewards(address stakingProvider) external view returns (uint96)
```

Returns the amount of rewards available for withdrawal for the
given staking provider. Reverts if staking provider has not
registered the operator address.

### pendingAuthorizationDecrease

```solidity
function pendingAuthorizationDecrease(address stakingProvider) external view returns (uint96)
```

Returns the amount of stake that is pending authorization
decrease for the given staking provider. If no authorization
decrease has been requested, returns zero.

### remainingAuthorizationDecreaseDelay

```solidity
function remainingAuthorizationDecreaseDelay(address stakingProvider) external view returns (uint64)
```

Returns the remaining time in seconds that needs to pass before
the requested authorization decrease can be approved.
If the sortition pool state was not updated yet by the operator
after requesting the authorization decrease, returns
`type(uint64).max`.

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

Checks if the operator's authorized stake is in sync with
operator's weight in the sortition pool.
If the operator is not in the sortition pool and their
authorized stake is non-zero, function returns false.

### isOperatorInPool

```solidity
function isOperatorInPool(address operator) external view returns (bool)
```

Returns true if the given operator is in the sortition pool.
Otherwise, returns false.

### selectGroup

```solidity
function selectGroup() external view returns (uint32[])
```

Selects a new group of operators. Can only be called when DKG
is in progress and the pool is locked.
At least one operator has to be registered in the pool,
otherwise the function fails reverting the transaction.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint32[] | IDs of selected group members. |

### authorizationParameters

```solidity
function authorizationParameters() external view returns (uint96 minimumAuthorization, uint64 authorizationDecreaseDelay, uint64 authorizationDecreaseChangePeriod)
```

Returns authorization-related parameters of the beacon.

The minimum authorization is also returned by `minimumAuthorization()`
function, as a requirement of `IApplication` interface.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| minimumAuthorization | uint96 | The minimum authorization amount required so that operator can participate in the random beacon. This amount is required to execute slashing for providing a malicious DKG result or when a relay entry times out. |
| authorizationDecreaseDelay | uint64 | Delay in seconds that needs to pass between the time authorization decrease is requested and the time that request gets approved. Protects against free-riders earning rewards and not being active in the network. |
| authorizationDecreaseChangePeriod | uint64 | Authorization decrease change period in seconds. It is the time, before authorization decrease delay end, during which the pending authorization decrease request can be overwritten. If set to 0, pending authorization decrease request can not be overwritten until the entire `authorizationDecreaseDelay` ends. If set to value equal `authorizationDecreaseDelay`, request can always be overwritten. |

### relayEntryParameters

```solidity
function relayEntryParameters() external view returns (uint256 relayEntrySoftTimeout, uint256 relayEntryHardTimeout, uint256 callbackGasLimit)
```

Returns relay-entry-related parameters of the beacon.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| relayEntrySoftTimeout | uint256 | Soft timeout in blocks for a group to submit the relay entry. If the soft timeout is reached for submitting the relay entry, the slashing starts. |
| relayEntryHardTimeout | uint256 | Hard timeout in blocks for a group to submit the relay entry. After the soft timeout passes without relay entry submitted, all group members start getting slashed. The slashing amount increases linearly until the group submits the relay entry or until `relayEntryHardTimeout` is reached. When the hard timeout is reached, each group member will get slashed for `_relayEntrySubmissionFailureSlashingAmount`. |
| callbackGasLimit | uint256 | Relay entry callback gas limit. This is the gas limit with which callback function provided in the relay request transaction is executed. The callback is executed with a new relay entry value in the same transaction the relay entry is submitted. |

### groupCreationParameters

```solidity
function groupCreationParameters() external view returns (uint256 groupCreationFrequency, uint256 groupLifetime, uint256 dkgResultChallengePeriodLength, uint256 dkgResultChallengeExtraGas, uint256 dkgResultSubmissionTimeout, uint256 dkgSubmitterPrecedencePeriodLength)
```

Returns group-creation-related parameters of the beacon.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| groupCreationFrequency | uint256 | The frequency of a new group creation. Groups are created with a fixed frequency of relay requests. |
| groupLifetime | uint256 | Group lifetime in blocks. When a group reached its lifetime, it is no longer selected for new relay requests but may still be responsible for submitting relay entry if relay request assigned to that group is still pending. |
| dkgResultChallengePeriodLength | uint256 | The number of blocks for which a DKG result can be challenged. Anyone can challenge DKG result for a certain number of blocks before the result is fully accepted and the group registered in the pool of active groups. If the challenge gets accepted, all operators who signed the malicious result get slashed for and the notifier gets rewarded. |
| dkgResultChallengeExtraGas | uint256 | The extra gas required to be left at the end of the challenge DKG result transaction. |
| dkgResultSubmissionTimeout | uint256 | Timeout in blocks for a group to submit the DKG result. All members are eligible to submit the DKG result. If `dkgResultSubmissionTimeout` passes without the DKG result submitted, DKG is considered as timed out and no DKG result for this group creation can be submitted anymore. |
| dkgSubmitterPrecedencePeriodLength | uint256 | Time during the DKG result approval stage when the submitter of the DKG result takes the precedence to approve the DKG result. After this time passes anyone can approve the DKG result. |

### rewardParameters

```solidity
function rewardParameters() external view returns (uint256 sortitionPoolRewardsBanDuration, uint256 relayEntryTimeoutNotificationRewardMultiplier, uint256 unauthorizedSigningNotificationRewardMultiplier, uint256 dkgMaliciousResultNotificationRewardMultiplier)
```

Returns reward-related parameters of the beacon.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| sortitionPoolRewardsBanDuration | uint256 | Duration of the sortition pool rewards ban imposed on operators who misbehaved during DKG by being inactive or disqualified and for operators that were identified by the rest of group members as inactive via `notifyOperatorInactivity`. |
| relayEntryTimeoutNotificationRewardMultiplier | uint256 | Percentage of the staking contract malicious behavior notification reward which will be transferred to the notifier reporting about relay entry timeout. Notifiers are rewarded from a notifiers treasury pool. For example, if notification reward is 1000 and the value of the multiplier is 5, the notifier will receive: 5% of 1000 = 50 per each operator affected. |
| unauthorizedSigningNotificationRewardMultiplier | uint256 | Percentage of the staking contract malicious behavior notification reward which will be transferred to the notifier reporting about unauthorized signing. Notifiers are rewarded from a notifiers treasury pool. For example, if a notification reward is 1000 and the value of the multiplier is 5, the notifier will receive: 5% of 1000 = 50 per each operator affected. |
| dkgMaliciousResultNotificationRewardMultiplier | uint256 | Percentage of the staking contract malicious behavior notification reward which will be transferred to the notifier reporting about a malicious DKG result. Notifiers are rewarded from a notifiers treasury pool. For example, if notification reward is 1000 and the value of the multiplier is 5, the notifier will receive: 5% of 1000 = 50 per each operator affected. |

### slashingParameters

```solidity
function slashingParameters() external view returns (uint96 relayEntrySubmissionFailureSlashingAmount, uint96 maliciousDkgResultSlashingAmount, uint96 unauthorizedSigningSlashingAmount)
```

Returns slashing-related parameters of the beacon.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| relayEntrySubmissionFailureSlashingAmount | uint96 | Slashing amount for not submitting relay entry. When relay entry hard timeout is reached without the relay entry submitted, each group member gets slashed for `relayEntrySubmissionFailureSlashingAmount`. If the relay entry gets submitted after the soft timeout, but before the hard timeout, each group member gets slashed proportionally to `relayEntrySubmissionFailureSlashingAmount` and the time passed since the soft deadline. |
| maliciousDkgResultSlashingAmount | uint96 | Slashing amount for submitting a malicious DKG result. Every DKG result submitted can be challenged for the time of `dkg.ResultChallengePeriodLength`. If the DKG result submitted is challenged and proven to be malicious, the operator who submitted the malicious result is slashed for `maliciousDkgResultSlashingAmount`. |
| unauthorizedSigningSlashingAmount | uint96 | Slashing amount when an unauthorized signing has been proved, which means the private key leaked and all the group members should be punished. |

### gasParameters

```solidity
function gasParameters() external view returns (uint256 dkgResultSubmissionGas, uint256 dkgResultApprovalGasOffset, uint256 notifyOperatorInactivityGasOffset, uint256 relayEntrySubmissionGasOffset)
```

Returns gas-related parameters of the beacon.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| dkgResultSubmissionGas | uint256 | Calculated gas cost for submitting a DKG result. This will be refunded as part of the DKG approval process. |
| dkgResultApprovalGasOffset | uint256 | Gas that is meant to balance the DKG result approval's overall cost. |
| notifyOperatorInactivityGasOffset | uint256 | Gas that is meant to balance the operator inactivity notification cost. |
| relayEntrySubmissionGasOffset | uint256 | Gas that is meant to balance the relay entry submission cost. |

