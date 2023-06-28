# Solidity API
## Table Of Contents

<!-- toc -->

- [BeaconDkgValidator](#beacondkgvalidator)
- [Governable](#governable)
- [RandomBeacon](#randombeacon)
- [RandomBeaconChaosnet](#randombeaconchaosnet)
- [RandomBeaconGovernance](#randombeacongovernance)
- [Reimbursable](#reimbursable)
- [ReimbursementPool](#reimbursementpool)
- [IRandomBeacon](#irandombeacon)
- [IRandomBeaconConsumer](#irandombeaconconsumer)
- [AltBn128](#altbn128)
- [BLS](#bls)
- [BeaconAuthorization](#beaconauthorization)
- [BeaconDkg](#beacondkg)
- [BeaconInactivity](#beaconinactivity)
- [BytesLib](#byteslib)
- [Callback](#callback)
- [Groups](#groups)
- [ModUtils](#modutils)
- [Relay](#relay)

<!-- tocstop -->

## BeaconDkgValidator

DKGValidator allows performing a full validation of DKG result,
including checking the format of fields in the result, declared
selected group members, and signatures of operators supporting the
result. The operator submitting the result should perform the
validation using a free contract call before submitting the result
to ensure their result is valid and can not be challenged. All other
network operators should perform validation of the submitted result
using a free contract call and challenge the result if the
validation fails.

### groupSize

```solidity
uint256 groupSize
```

Size of a group in the threshold relay.

### groupThreshold

```solidity
uint256 groupThreshold
```

The minimum number of group members needed to interact according to
the protocol to produce a relay entry. The adversary can not learn
anything about the key as long as it does not break into
groupThreshold+1 of members.

### activeThreshold

```solidity
uint256 activeThreshold
```

The minimum number of active and properly behaving group members
during the DKG needed to accept the result. This number is higher
than `groupThreshold` to keep a safety margin for members becoming
inactive after DKG so that the group can still produce a relay
entry.

### signatureByteSize

```solidity
uint256 signatureByteSize
```

Size in bytes of a single signature produced by operator supporting
DKG result.

### sortitionPool

```solidity
contract SortitionPool sortitionPool
```

### constructor

```solidity
constructor(contract SortitionPool _sortitionPool) public
```

### validate

```solidity
function validate(struct BeaconDkg.Result result, uint256 seed, uint256 startBlock) external view returns (bool isValid, string errorMsg)
```

Performs a full validation of DKG result, including checking the
format of fields in the result, declared selected group members,
and signatures of operators supporting the result.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| result | struct BeaconDkg.Result |  |
| seed | uint256 | seed used to start the DKG and select group members |
| startBlock | uint256 | DKG start block |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| isValid | bool | true if the result is valid, false otherwise |
| errorMsg | string | validation error message; empty for a valid result |

### validateFields

```solidity
function validateFields(struct BeaconDkg.Result result) public pure returns (bool isValid, string errorMsg)
```

Performs a static validation of DKG result fields: lengths,
ranges, and order of arrays.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| isValid | bool | true if the result is valid, false otherwise |
| errorMsg | string | validation error message; empty for a valid result |

### validateGroupMembers

```solidity
function validateGroupMembers(struct BeaconDkg.Result result, uint256 seed) public view returns (bool)
```

Performs validation of group members as declared in DKG
result against group members selected by the sortition pool.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| result | struct BeaconDkg.Result |  |
| seed | uint256 | seed used to start the DKG and select group members |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | true if group members matches; false otherwise |

### validateSignatures

```solidity
function validateSignatures(struct BeaconDkg.Result result, uint256 startBlock) public view returns (bool)
```

Performs validation of signatures supplied in DKG result.
Note that this function does not check if addresses which
supplied signatures supporting the result are the ones selected
to the group by sortition pool. This function should be used
together with `validateGroupMembers`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| result | struct BeaconDkg.Result |  |
| startBlock | uint256 | DKG start block |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | true if group members matches; false otherwise |

### validateMembersHash

```solidity
function validateMembersHash(struct BeaconDkg.Result result) public pure returns (bool)
```

Performs validation of hashed group members that actively took
part in DKG.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| result | struct BeaconDkg.Result | DKG result |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | true if result's group members hash matches with the one that is challenged. |

## Governable

Governable contract.

A constructor is not defined, which makes the contract compatible with
upgradable proxies. This requires calling explicitly `_transferGovernance`
function in a child contract.

### governance

```solidity
address governance
```

### GovernanceTransferred

```solidity
event GovernanceTransferred(address oldGovernance, address newGovernance)
```

### onlyGovernance

```solidity
modifier onlyGovernance()
```

### transferGovernance

```solidity
function transferGovernance(address newGovernance) external virtual
```

Transfers governance of the contract to `newGovernance`.

### _transferGovernance

```solidity
function _transferGovernance(address newGovernance) internal virtual
```

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

## RandomBeaconChaosnet

A stub contract that will be used temporarily until the real-world
random beacon client implementation is ready.

### authorizedRequesters

```solidity
mapping(address => bool) authorizedRequesters
```

Authorized addresses that can request a relay entry.

### entry

```solidity
uint256 entry
```

Arbitrary relay entry. Initially set to the Euler's number.
It's updated after each relay entry request.

### RequesterAuthorizationUpdated

```solidity
event RequesterAuthorizationUpdated(address requester, bool isAuthorized)
```

### requestRelayEntry

```solidity
function requestRelayEntry(contract IRandomBeaconConsumer callbackContract) external
```

Executes the callback with an arbitrary relay entry number.

The caller must be an authorized requester.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| callbackContract | contract IRandomBeaconConsumer | Beacon consumer callback contract - Wallet Registry |

### setRequesterAuthorization

```solidity
function setRequesterAuthorization(address requester, bool isAuthorized) external
```

Authorizes a requester of the relay entry.

## RandomBeaconGovernance

Owns the `RandomBeacon` contract and is responsible for updating its
governable parameters in respect to governance delay individual
for each parameter.

### newGovernanceDelay

```solidity
uint256 newGovernanceDelay
```

### governanceDelayChangeInitiated

```solidity
uint256 governanceDelayChangeInitiated
```

### newRandomBeaconGovernance

```solidity
address newRandomBeaconGovernance
```

### randomBeaconGovernanceTransferInitiated

```solidity
uint256 randomBeaconGovernanceTransferInitiated
```

### newRelayEntrySoftTimeout

```solidity
uint256 newRelayEntrySoftTimeout
```

### relayEntrySoftTimeoutChangeInitiated

```solidity
uint256 relayEntrySoftTimeoutChangeInitiated
```

### newRelayEntryHardTimeout

```solidity
uint256 newRelayEntryHardTimeout
```

### relayEntryHardTimeoutChangeInitiated

```solidity
uint256 relayEntryHardTimeoutChangeInitiated
```

### newCallbackGasLimit

```solidity
uint256 newCallbackGasLimit
```

### callbackGasLimitChangeInitiated

```solidity
uint256 callbackGasLimitChangeInitiated
```

### newGroupCreationFrequency

```solidity
uint256 newGroupCreationFrequency
```

### groupCreationFrequencyChangeInitiated

```solidity
uint256 groupCreationFrequencyChangeInitiated
```

### newGroupLifetime

```solidity
uint256 newGroupLifetime
```

### groupLifetimeChangeInitiated

```solidity
uint256 groupLifetimeChangeInitiated
```

### newDkgResultChallengePeriodLength

```solidity
uint256 newDkgResultChallengePeriodLength
```

### dkgResultChallengePeriodLengthChangeInitiated

```solidity
uint256 dkgResultChallengePeriodLengthChangeInitiated
```

### newDkgResultChallengeExtraGas

```solidity
uint256 newDkgResultChallengeExtraGas
```

### dkgResultChallengeExtraGasChangeInitiated

```solidity
uint256 dkgResultChallengeExtraGasChangeInitiated
```

### newDkgResultSubmissionTimeout

```solidity
uint256 newDkgResultSubmissionTimeout
```

### dkgResultSubmissionTimeoutChangeInitiated

```solidity
uint256 dkgResultSubmissionTimeoutChangeInitiated
```

### newDkgSubmitterPrecedencePeriodLength

```solidity
uint256 newDkgSubmitterPrecedencePeriodLength
```

### dkgSubmitterPrecedencePeriodLengthChangeInitiated

```solidity
uint256 dkgSubmitterPrecedencePeriodLengthChangeInitiated
```

### newRelayEntrySubmissionFailureSlashingAmount

```solidity
uint96 newRelayEntrySubmissionFailureSlashingAmount
```

### relayEntrySubmissionFailureSlashingAmountChangeInitiated

```solidity
uint256 relayEntrySubmissionFailureSlashingAmountChangeInitiated
```

### newMaliciousDkgResultSlashingAmount

```solidity
uint96 newMaliciousDkgResultSlashingAmount
```

### maliciousDkgResultSlashingAmountChangeInitiated

```solidity
uint256 maliciousDkgResultSlashingAmountChangeInitiated
```

### newUnauthorizedSigningSlashingAmount

```solidity
uint96 newUnauthorizedSigningSlashingAmount
```

### unauthorizedSigningSlashingAmountChangeInitiated

```solidity
uint256 unauthorizedSigningSlashingAmountChangeInitiated
```

### newSortitionPoolRewardsBanDuration

```solidity
uint256 newSortitionPoolRewardsBanDuration
```

### sortitionPoolRewardsBanDurationChangeInitiated

```solidity
uint256 sortitionPoolRewardsBanDurationChangeInitiated
```

### newRelayEntryTimeoutNotificationRewardMultiplier

```solidity
uint256 newRelayEntryTimeoutNotificationRewardMultiplier
```

### relayEntryTimeoutNotificationRewardMultiplierChangeInitiated

```solidity
uint256 relayEntryTimeoutNotificationRewardMultiplierChangeInitiated
```

### newUnauthorizedSigningNotificationRewardMultiplier

```solidity
uint256 newUnauthorizedSigningNotificationRewardMultiplier
```

### unauthorizedSigningNotificationRewardMultiplierChangeInitiated

```solidity
uint256 unauthorizedSigningNotificationRewardMultiplierChangeInitiated
```

### newMinimumAuthorization

```solidity
uint96 newMinimumAuthorization
```

### minimumAuthorizationChangeInitiated

```solidity
uint256 minimumAuthorizationChangeInitiated
```

### newAuthorizationDecreaseDelay

```solidity
uint64 newAuthorizationDecreaseDelay
```

### authorizationDecreaseDelayChangeInitiated

```solidity
uint256 authorizationDecreaseDelayChangeInitiated
```

### newAuthorizationDecreaseChangePeriod

```solidity
uint64 newAuthorizationDecreaseChangePeriod
```

### authorizationDecreaseChangePeriodChangeInitiated

```solidity
uint256 authorizationDecreaseChangePeriodChangeInitiated
```

### newDkgMaliciousResultNotificationRewardMultiplier

```solidity
uint256 newDkgMaliciousResultNotificationRewardMultiplier
```

### dkgMaliciousResultNotificationRewardMultiplierChangeInitiated

```solidity
uint256 dkgMaliciousResultNotificationRewardMultiplierChangeInitiated
```

### newDkgResultSubmissionGas

```solidity
uint256 newDkgResultSubmissionGas
```

### dkgResultSubmissionGasChangeInitiated

```solidity
uint256 dkgResultSubmissionGasChangeInitiated
```

### newDkgResultApprovalGasOffset

```solidity
uint256 newDkgResultApprovalGasOffset
```

### dkgResultApprovalGasOffsetChangeInitiated

```solidity
uint256 dkgResultApprovalGasOffsetChangeInitiated
```

### newNotifyOperatorInactivityGasOffset

```solidity
uint256 newNotifyOperatorInactivityGasOffset
```

### notifyOperatorInactivityGasOffsetChangeInitiated

```solidity
uint256 notifyOperatorInactivityGasOffsetChangeInitiated
```

### newRelayEntrySubmissionGasOffset

```solidity
uint256 newRelayEntrySubmissionGasOffset
```

### relayEntrySubmissionGasOffsetChangeInitiated

```solidity
uint256 relayEntrySubmissionGasOffsetChangeInitiated
```

### randomBeacon

```solidity
contract RandomBeacon randomBeacon
```

### governanceDelay

```solidity
uint256 governanceDelay
```

### GovernanceDelayUpdateStarted

```solidity
event GovernanceDelayUpdateStarted(uint256 governanceDelay, uint256 timestamp)
```

### GovernanceDelayUpdated

```solidity
event GovernanceDelayUpdated(uint256 governanceDelay)
```

### RandomBeaconGovernanceTransferStarted

```solidity
event RandomBeaconGovernanceTransferStarted(address newRandomBeaconGovernance, uint256 timestamp)
```

### RandomBeaconGovernanceTransferred

```solidity
event RandomBeaconGovernanceTransferred(address newRandomBeaconGovernance)
```

### RelayEntrySoftTimeoutUpdateStarted

```solidity
event RelayEntrySoftTimeoutUpdateStarted(uint256 relayEntrySoftTimeout, uint256 timestamp)
```

### RelayEntrySoftTimeoutUpdated

```solidity
event RelayEntrySoftTimeoutUpdated(uint256 relayEntrySoftTimeout)
```

### RelayEntryHardTimeoutUpdateStarted

```solidity
event RelayEntryHardTimeoutUpdateStarted(uint256 relayEntryHardTimeout, uint256 timestamp)
```

### RelayEntryHardTimeoutUpdated

```solidity
event RelayEntryHardTimeoutUpdated(uint256 relayEntryHardTimeout)
```

### CallbackGasLimitUpdateStarted

```solidity
event CallbackGasLimitUpdateStarted(uint256 callbackGasLimit, uint256 timestamp)
```

### CallbackGasLimitUpdated

```solidity
event CallbackGasLimitUpdated(uint256 callbackGasLimit)
```

### GroupCreationFrequencyUpdateStarted

```solidity
event GroupCreationFrequencyUpdateStarted(uint256 groupCreationFrequency, uint256 timestamp)
```

### GroupCreationFrequencyUpdated

```solidity
event GroupCreationFrequencyUpdated(uint256 groupCreationFrequency)
```

### GroupLifetimeUpdateStarted

```solidity
event GroupLifetimeUpdateStarted(uint256 groupLifetime, uint256 timestamp)
```

### GroupLifetimeUpdated

```solidity
event GroupLifetimeUpdated(uint256 groupLifetime)
```

### DkgResultChallengePeriodLengthUpdateStarted

```solidity
event DkgResultChallengePeriodLengthUpdateStarted(uint256 dkgResultChallengePeriodLength, uint256 timestamp)
```

### DkgResultChallengePeriodLengthUpdated

```solidity
event DkgResultChallengePeriodLengthUpdated(uint256 dkgResultChallengePeriodLength)
```

### DkgResultChallengeExtraGasUpdateStarted

```solidity
event DkgResultChallengeExtraGasUpdateStarted(uint256 dkgResultChallengeExtraGas, uint256 timestamp)
```

### DkgResultChallengeExtraGasUpdated

```solidity
event DkgResultChallengeExtraGasUpdated(uint256 dkgResultChallengeExtraGas)
```

### DkgResultSubmissionTimeoutUpdateStarted

```solidity
event DkgResultSubmissionTimeoutUpdateStarted(uint256 dkgResultSubmissionTimeout, uint256 timestamp)
```

### DkgResultSubmissionTimeoutUpdated

```solidity
event DkgResultSubmissionTimeoutUpdated(uint256 dkgResultSubmissionTimeout)
```

### DkgSubmitterPrecedencePeriodLengthUpdateStarted

```solidity
event DkgSubmitterPrecedencePeriodLengthUpdateStarted(uint256 submitterPrecedencePeriodLength, uint256 timestamp)
```

### DkgSubmitterPrecedencePeriodLengthUpdated

```solidity
event DkgSubmitterPrecedencePeriodLengthUpdated(uint256 submitterPrecedencePeriodLength)
```

### RelayEntrySubmissionFailureSlashingAmountUpdateStarted

```solidity
event RelayEntrySubmissionFailureSlashingAmountUpdateStarted(uint96 relayEntrySubmissionFailureSlashingAmount, uint256 timestamp)
```

### RelayEntrySubmissionFailureSlashingAmountUpdated

```solidity
event RelayEntrySubmissionFailureSlashingAmountUpdated(uint96 relayEntrySubmissionFailureSlashingAmount)
```

### MaliciousDkgResultSlashingAmountUpdateStarted

```solidity
event MaliciousDkgResultSlashingAmountUpdateStarted(uint96 maliciousDkgResultSlashingAmount, uint256 timestamp)
```

### MaliciousDkgResultSlashingAmountUpdated

```solidity
event MaliciousDkgResultSlashingAmountUpdated(uint96 maliciousDkgResultSlashingAmount)
```

### UnauthorizedSigningSlashingAmountUpdateStarted

```solidity
event UnauthorizedSigningSlashingAmountUpdateStarted(uint96 unauthorizedSigningSlashingAmount, uint256 timestamp)
```

### UnauthorizedSigningSlashingAmountUpdated

```solidity
event UnauthorizedSigningSlashingAmountUpdated(uint96 unauthorizedSigningSlashingAmount)
```

### SortitionPoolRewardsBanDurationUpdateStarted

```solidity
event SortitionPoolRewardsBanDurationUpdateStarted(uint256 sortitionPoolRewardsBanDuration, uint256 timestamp)
```

### SortitionPoolRewardsBanDurationUpdated

```solidity
event SortitionPoolRewardsBanDurationUpdated(uint256 sortitionPoolRewardsBanDuration)
```

### RelayEntryTimeoutNotificationRewardMultiplierUpdateStarted

```solidity
event RelayEntryTimeoutNotificationRewardMultiplierUpdateStarted(uint256 relayEntryTimeoutNotificationRewardMultiplier, uint256 timestamp)
```

### RelayEntryTimeoutNotificationRewardMultiplierUpdated

```solidity
event RelayEntryTimeoutNotificationRewardMultiplierUpdated(uint256 relayEntryTimeoutNotificationRewardMultiplier)
```

### UnauthorizedSigningNotificationRewardMultiplierUpdateStarted

```solidity
event UnauthorizedSigningNotificationRewardMultiplierUpdateStarted(uint256 unauthorizedSigningTimeoutNotificationRewardMultiplier, uint256 timestamp)
```

### UnauthorizedSigningNotificationRewardMultiplierUpdated

```solidity
event UnauthorizedSigningNotificationRewardMultiplierUpdated(uint256 unauthorizedSigningTimeoutNotificationRewardMultiplier)
```

### MinimumAuthorizationUpdateStarted

```solidity
event MinimumAuthorizationUpdateStarted(uint96 minimumAuthorization, uint256 timestamp)
```

### MinimumAuthorizationUpdated

```solidity
event MinimumAuthorizationUpdated(uint96 minimumAuthorization)
```

### AuthorizationDecreaseDelayUpdateStarted

```solidity
event AuthorizationDecreaseDelayUpdateStarted(uint64 authorizationDecreaseDelay, uint256 timestamp)
```

### AuthorizationDecreaseDelayUpdated

```solidity
event AuthorizationDecreaseDelayUpdated(uint64 authorizationDecreaseDelay)
```

### AuthorizationDecreaseChangePeriodUpdateStarted

```solidity
event AuthorizationDecreaseChangePeriodUpdateStarted(uint64 authorizationDecreaseChangePeriod, uint256 timestamp)
```

### AuthorizationDecreaseChangePeriodUpdated

```solidity
event AuthorizationDecreaseChangePeriodUpdated(uint64 authorizationDecreaseChangePeriod)
```

### DkgMaliciousResultNotificationRewardMultiplierUpdateStarted

```solidity
event DkgMaliciousResultNotificationRewardMultiplierUpdateStarted(uint256 dkgMaliciousResultNotificationRewardMultiplier, uint256 timestamp)
```

### DkgMaliciousResultNotificationRewardMultiplierUpdated

```solidity
event DkgMaliciousResultNotificationRewardMultiplierUpdated(uint256 dkgMaliciousResultNotificationRewardMultiplier)
```

### DkgResultSubmissionGasUpdateStarted

```solidity
event DkgResultSubmissionGasUpdateStarted(uint256 dkgResultSubmissionGas, uint256 timestamp)
```

### DkgResultSubmissionGasUpdated

```solidity
event DkgResultSubmissionGasUpdated(uint256 dkgResultSubmissionGas)
```

### DkgResultApprovalGasOffsetUpdateStarted

```solidity
event DkgResultApprovalGasOffsetUpdateStarted(uint256 dkgResultApprovalGasOffset, uint256 timestamp)
```

### DkgResultApprovalGasOffsetUpdated

```solidity
event DkgResultApprovalGasOffsetUpdated(uint256 dkgResultApprovalGasOffset)
```

### NotifyOperatorInactivityGasOffsetUpdateStarted

```solidity
event NotifyOperatorInactivityGasOffsetUpdateStarted(uint256 notifyOperatorInactivityGasOffset, uint256 timestamp)
```

### NotifyOperatorInactivityGasOffsetUpdated

```solidity
event NotifyOperatorInactivityGasOffsetUpdated(uint256 notifyOperatorInactivityGasOffset)
```

### RelayEntrySubmissionGasOffsetUpdateStarted

```solidity
event RelayEntrySubmissionGasOffsetUpdateStarted(uint256 relayEntrySubmissionGasOffset, uint256 timestamp)
```

### RelayEntrySubmissionGasOffsetUpdated

```solidity
event RelayEntrySubmissionGasOffsetUpdated(uint256 relayEntrySubmissionGasOffset)
```

### onlyAfterGovernanceDelay

```solidity
modifier onlyAfterGovernanceDelay(uint256 changeInitiatedTimestamp)
```

Reverts if called before the governance delay elapses.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| changeInitiatedTimestamp | uint256 | Timestamp indicating the beginning of the change. |

### constructor

```solidity
constructor(contract RandomBeacon _randomBeacon, uint256 _governanceDelay) public
```

### beginGovernanceDelayUpdate

```solidity
function beginGovernanceDelayUpdate(uint256 _newGovernanceDelay) external
```

Begins the governance delay update process.

Can be called only by the contract owner.

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
delay elapses.

### beginRandomBeaconGovernanceTransfer

```solidity
function beginRandomBeaconGovernanceTransfer(address _newRandomBeaconGovernance) external
```

Begins the random beacon governance transfer process.

Can be called only by the current contract governance.

### finalizeRandomBeaconGovernanceTransfer

```solidity
function finalizeRandomBeaconGovernanceTransfer() external
```

Finalizes the random beacon governance transfer process.

Can be called only by the current contract governance, after the
governance delay elapses.

### beginRelayEntrySoftTimeoutUpdate

```solidity
function beginRelayEntrySoftTimeoutUpdate(uint256 _newRelayEntrySoftTimeout) external
```

Begins the relay entry soft timeout update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRelayEntrySoftTimeout | uint256 | New relay entry submission timeout in blocks |

### finalizeRelayEntrySoftTimeoutUpdate

```solidity
function finalizeRelayEntrySoftTimeoutUpdate() external
```

Finalizes the relay entry soft timeout update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRelayEntryHardTimeoutUpdate

```solidity
function beginRelayEntryHardTimeoutUpdate(uint256 _newRelayEntryHardTimeout) external
```

Begins the relay entry hard timeout update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRelayEntryHardTimeout | uint256 | New relay entry hard timeout in blocks |

### finalizeRelayEntryHardTimeoutUpdate

```solidity
function finalizeRelayEntryHardTimeoutUpdate() external
```

Finalizes the relay entry hard timeout update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginCallbackGasLimitUpdate

```solidity
function beginCallbackGasLimitUpdate(uint256 _newCallbackGasLimit) external
```

Begins the callback gas limit update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newCallbackGasLimit | uint256 | New callback gas limit |

### finalizeCallbackGasLimitUpdate

```solidity
function finalizeCallbackGasLimitUpdate() external
```

Finalizes the callback gas limit update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginGroupCreationFrequencyUpdate

```solidity
function beginGroupCreationFrequencyUpdate(uint256 _newGroupCreationFrequency) external
```

Begins the group creation frequency update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newGroupCreationFrequency | uint256 | New group creation frequency |

### finalizeGroupCreationFrequencyUpdate

```solidity
function finalizeGroupCreationFrequencyUpdate() external
```

Finalizes the group creation frequency update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginGroupLifetimeUpdate

```solidity
function beginGroupLifetimeUpdate(uint256 _newGroupLifetime) external
```

Begins the group lifetime update process. Group lifetime needs to
be shorter than the authorization decrease delay to ensure every
active group is backed by enough stake. A new group lifetime value
is in blocks and has to be calculated based on the average block
time and authorization decrease delay which value is in seconds.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newGroupLifetime | uint256 | New group lifetime in blocks |

### finalizeGroupLifetimeUpdate

```solidity
function finalizeGroupLifetimeUpdate() external
```

Finalizes the group creation frequency update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginDkgResultChallengePeriodLengthUpdate

```solidity
function beginDkgResultChallengePeriodLengthUpdate(uint256 _newDkgResultChallengePeriodLength) external
```

Begins the DKG result challenge period length update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDkgResultChallengePeriodLength | uint256 | New DKG result challenge period length in blocks |

### finalizeDkgResultChallengePeriodLengthUpdate

```solidity
function finalizeDkgResultChallengePeriodLengthUpdate() external
```

Finalizes the DKG result challenge period length update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginDkgResultChallengeExtraGasUpdate

```solidity
function beginDkgResultChallengeExtraGasUpdate(uint256 _newDkgResultChallengeExtraGas) external
```

Begins the DKG result challenge extra gas update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDkgResultChallengeExtraGas | uint256 | New DKG result challenge extra gas |

### finalizeDkgResultChallengeExtraGasUpdate

```solidity
function finalizeDkgResultChallengeExtraGasUpdate() external
```

Finalizes the DKG result challenge extra gas update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginDkgResultSubmissionTimeoutUpdate

```solidity
function beginDkgResultSubmissionTimeoutUpdate(uint256 _newDkgResultSubmissionTimeout) external
```

Begins the DKG result submission timeout update
process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDkgResultSubmissionTimeout | uint256 | New DKG result submission timeout in blocks |

### finalizeDkgResultSubmissionTimeoutUpdate

```solidity
function finalizeDkgResultSubmissionTimeoutUpdate() external
```

Finalizes the DKG result submission timeout update
process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginDkgSubmitterPrecedencePeriodLengthUpdate

```solidity
function beginDkgSubmitterPrecedencePeriodLengthUpdate(uint256 _newDkgSubmitterPrecedencePeriodLength) external
```

Begins the DKG submitter precedence period length.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDkgSubmitterPrecedencePeriodLength | uint256 | New DKG submitter precedence period length in blocks |

### finalizeDkgSubmitterPrecedencePeriodLengthUpdate

```solidity
function finalizeDkgSubmitterPrecedencePeriodLengthUpdate() external
```

Finalizes the DKG submitter precedence period length.

Can be called only by the contract owner, after the governance
delay elapses.

### beginSortitionPoolRewardsBanDurationUpdate

```solidity
function beginSortitionPoolRewardsBanDurationUpdate(uint256 _newSortitionPoolRewardsBanDuration) external
```

Begins the sortition pool rewards ban duration update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newSortitionPoolRewardsBanDuration | uint256 | New sortition pool rewards ban duration. |

### finalizeSortitionPoolRewardsBanDurationUpdate

```solidity
function finalizeSortitionPoolRewardsBanDurationUpdate() external
```

Finalizes the sortition pool rewards ban duration update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRelayEntryTimeoutNotificationRewardMultiplierUpdate

```solidity
function beginRelayEntryTimeoutNotificationRewardMultiplierUpdate(uint256 _newRelayEntryTimeoutNotificationRewardMultiplier) external
```

Begins the relay entry timeout notification reward multiplier
update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRelayEntryTimeoutNotificationRewardMultiplier | uint256 | New relay entry timeout notification reward multiplier. |

### beginUnauthorizedSigningNotificationRewardMultiplierUpdate

```solidity
function beginUnauthorizedSigningNotificationRewardMultiplierUpdate(uint256 _newUnauthorizedSigningNotificationRewardMultiplier) external
```

Begins the unauthorized signing notification reward multiplier
update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newUnauthorizedSigningNotificationRewardMultiplier | uint256 | New unauthorized signing notification reward multiplier. |

### finalizeUnauthorizedSigningNotificationRewardMultiplierUpdate

```solidity
function finalizeUnauthorizedSigningNotificationRewardMultiplierUpdate() external
```

Finalizes the unauthorized signing notification reward
multiplier update process.

Can be called only by the contract owner, after the governance
delay elapses.

### finalizeRelayEntryTimeoutNotificationRewardMultiplierUpdate

```solidity
function finalizeRelayEntryTimeoutNotificationRewardMultiplierUpdate() external
```

Finalizes the relay entry timeout notification reward
multiplier update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginDkgMaliciousResultNotificationRewardMultiplierUpdate

```solidity
function beginDkgMaliciousResultNotificationRewardMultiplierUpdate(uint256 _newDkgMaliciousResultNotificationRewardMultiplier) external
```

Begins the DKG malicious result notification reward multiplier
update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDkgMaliciousResultNotificationRewardMultiplier | uint256 | New DKG malicious result notification reward multiplier. |

### finalizeDkgMaliciousResultNotificationRewardMultiplierUpdate

```solidity
function finalizeDkgMaliciousResultNotificationRewardMultiplierUpdate() external
```

Finalizes the DKG malicious result notification reward
multiplier update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRelayEntrySubmissionFailureSlashingAmountUpdate

```solidity
function beginRelayEntrySubmissionFailureSlashingAmountUpdate(uint96 _newRelayEntrySubmissionFailureSlashingAmount) external
```

Begins the relay entry submission failure slashing amount update
process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRelayEntrySubmissionFailureSlashingAmount | uint96 | New relay entry submission failure slashing amount |

### finalizeRelayEntrySubmissionFailureSlashingAmountUpdate

```solidity
function finalizeRelayEntrySubmissionFailureSlashingAmountUpdate() external
```

Finalizes the relay entry submission failure slashing amount
update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginDkgResultSubmissionGasUpdate

```solidity
function beginDkgResultSubmissionGasUpdate(uint256 _newDkgResultSubmissionGas) external
```

Begins the DKG result submission gas update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDkgResultSubmissionGas | uint256 | New relay entry submission gas offset |

### finalizeDkgResultSubmissionGasUpdate

```solidity
function finalizeDkgResultSubmissionGasUpdate() external
```

Finalizes DKG result submission gas update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginDkgResultApprovalGasOffsetUpdate

```solidity
function beginDkgResultApprovalGasOffsetUpdate(uint256 _newDkgResultApprovalGasOffset) external
```

Begins the DKG result approval gas offset update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDkgResultApprovalGasOffset | uint256 | New DKG approval gas offset |

### finalizeDkgResultApprovalGasOffsetUpdate

```solidity
function finalizeDkgResultApprovalGasOffsetUpdate() external
```

Finalizes the DKG result approval gas offset update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginNotifyOperatorInactivityGasOffsetUpdate

```solidity
function beginNotifyOperatorInactivityGasOffsetUpdate(uint256 _newNotifyOperatorInactivityGasOffset) external
```

Begins the notify operator inactivity gas offset update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newNotifyOperatorInactivityGasOffset | uint256 | New operator inactivity notification gas offset |

### finalizeNotifyOperatorInactivityGasOffsetUpdate

```solidity
function finalizeNotifyOperatorInactivityGasOffsetUpdate() external
```

Finalizes the notify operator inactivity gas offset update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRelayEntrySubmissionGasOffsetUpdate

```solidity
function beginRelayEntrySubmissionGasOffsetUpdate(uint256 _newRelayEntrySubmissionGasOffset) external
```

Begins the relay entry submission gas offset update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRelayEntrySubmissionGasOffset | uint256 | New relay entry submission gas offset |

### finalizeRelayEntrySubmissionGasOffsetUpdate

```solidity
function finalizeRelayEntrySubmissionGasOffsetUpdate() external
```

Finalizes relay entry submission gas offset update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMaliciousDkgResultSlashingAmountUpdate

```solidity
function beginMaliciousDkgResultSlashingAmountUpdate(uint96 _newMaliciousDkgResultSlashingAmount) external
```

Begins the malicious DKG result slashing amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMaliciousDkgResultSlashingAmount | uint96 | New malicious DKG result slashing amount |

### finalizeMaliciousDkgResultSlashingAmountUpdate

```solidity
function finalizeMaliciousDkgResultSlashingAmountUpdate() external
```

Finalizes the malicious DKG result slashing amount update
process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginUnauthorizedSigningSlashingAmountUpdate

```solidity
function beginUnauthorizedSigningSlashingAmountUpdate(uint96 _newUnauthorizedSigningSlashingAmount) external
```

Begins the unauthorized signing slashing amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newUnauthorizedSigningSlashingAmount | uint96 | New unauthorized signing slashing amount |

### finalizeUnauthorizedSigningSlashingAmountUpdate

```solidity
function finalizeUnauthorizedSigningSlashingAmountUpdate() external
```

Finalizes the unauthorized signing slashing amount update
process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMinimumAuthorizationUpdate

```solidity
function beginMinimumAuthorizationUpdate(uint96 _newMinimumAuthorization) external
```

Begins the minimum authorization amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMinimumAuthorization | uint96 | New minimum authorization amount. |

### finalizeMinimumAuthorizationUpdate

```solidity
function finalizeMinimumAuthorizationUpdate() external
```

Finalizes the minimum authorization amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginAuthorizationDecreaseDelayUpdate

```solidity
function beginAuthorizationDecreaseDelayUpdate(uint64 _newAuthorizationDecreaseDelay) external
```

Begins the authorization decrease delay update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newAuthorizationDecreaseDelay | uint64 | New authorization decrease delay |

### finalizeAuthorizationDecreaseDelayUpdate

```solidity
function finalizeAuthorizationDecreaseDelayUpdate() external
```

Finalizes the authorization decrease delay update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginAuthorizationDecreaseChangePeriodUpdate

```solidity
function beginAuthorizationDecreaseChangePeriodUpdate(uint64 _newAuthorizationDecreaseChangePeriod) external
```

Begins the authorization decrease change period update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newAuthorizationDecreaseChangePeriod | uint64 | New authorization decrease change period |

### finalizeAuthorizationDecreaseChangePeriodUpdate

```solidity
function finalizeAuthorizationDecreaseChangePeriodUpdate() external
```

Finalizes the authorization decrease change period update process.

Can be called only by the contract owner, after the governance
delay elapses.

### setRequesterAuthorization

```solidity
function setRequesterAuthorization(address requester, bool isAuthorized) external
```

Set authorization for requesters that can request a relay
entry. It can be done by the governance only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| requester | address | Requester, can be a contract or EOA |
| isAuthorized | bool | True or false |

### withdrawIneligibleRewards

```solidity
function withdrawIneligibleRewards(address recipient) external
```

Withdraws rewards belonging to operators marked as ineligible
for sortition pool rewards.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| recipient | address | Recipient of withdrawn rewards. |

### getRemainingGovernanceDelayUpdateTime

```solidity
function getRemainingGovernanceDelayUpdateTime() external view returns (uint256)
```

Get the time remaining until the governance delay can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingRandomBeaconGovernanceTransferDelayTime

```solidity
function getRemainingRandomBeaconGovernanceTransferDelayTime() external view returns (uint256)
```

Get the time remaining until the random beacon governance can
be transferred.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingRelayEntrySoftTimeoutUpdateTime

```solidity
function getRemainingRelayEntrySoftTimeoutUpdateTime() external view returns (uint256)
```

Get the time remaining until the relay entry submission soft
timeout can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingRelayEntryHardTimeoutUpdateTime

```solidity
function getRemainingRelayEntryHardTimeoutUpdateTime() external view returns (uint256)
```

Get the time remaining until the relay entry hard timeout can be
updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingCallbackGasLimitUpdateTime

```solidity
function getRemainingCallbackGasLimitUpdateTime() external view returns (uint256)
```

Get the time remaining until the callback gas limit can be
updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingGroupCreationFrequencyUpdateTime

```solidity
function getRemainingGroupCreationFrequencyUpdateTime() external view returns (uint256)
```

Get the time remaining until the group creation frequency can be
updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingGroupLifetimeUpdateTime

```solidity
function getRemainingGroupLifetimeUpdateTime() external view returns (uint256)
```

Get the time remaining until the group lifetime can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingDkgResultChallengePeriodLengthUpdateTime

```solidity
function getRemainingDkgResultChallengePeriodLengthUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG result challenge period
length can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingDkgResultChallengeExtraGasUpdateTime

```solidity
function getRemainingDkgResultChallengeExtraGasUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG result challenge extra
gas can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingDkgResultSubmissionTimeoutUpdateTime

```solidity
function getRemainingDkgResultSubmissionTimeoutUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG result submission timeout
can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingDkgSubmitterPrecedencePeriodLengthUpdateTime

```solidity
function getRemainingDkgSubmitterPrecedencePeriodLengthUpdateTime() external view returns (uint256)
```

Get the time remaining until the wallet owner can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingRelayEntrySubmissionFailureSlashingAmountUpdateTime

```solidity
function getRemainingRelayEntrySubmissionFailureSlashingAmountUpdateTime() external view returns (uint256)
```

Get the time remaining until the relay entry submission failure
slashing amount can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingMaliciousDkgResultSlashingAmountUpdateTime

```solidity
function getRemainingMaliciousDkgResultSlashingAmountUpdateTime() external view returns (uint256)
```

Get the time remaining until the malicious DKG result
slashing amount can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingUnauthorizedSigningSlashingAmountUpdateTime

```solidity
function getRemainingUnauthorizedSigningSlashingAmountUpdateTime() external view returns (uint256)
```

Get the time remaining until the unauthorized signing
slashing amount can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingMimimumAuthorizationUpdateTime

```solidity
function getRemainingMimimumAuthorizationUpdateTime() external view returns (uint256)
```

Get the time remaining until the minimum authorization amount
can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingAuthorizationDecreaseDelayUpdateTime

```solidity
function getRemainingAuthorizationDecreaseDelayUpdateTime() external view returns (uint256)
```

Get the time remaining until the authorization decrease delay
can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingAuthorizationDecreaseChangePeriodUpdateTime

```solidity
function getRemainingAuthorizationDecreaseChangePeriodUpdateTime() external view returns (uint256)
```

Get the time remaining until the authorization decrease change
period can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingSortitionPoolRewardsBanDurationUpdateTime

```solidity
function getRemainingSortitionPoolRewardsBanDurationUpdateTime() external view returns (uint256)
```

Get the time remaining until the sortition pool rewards ban
duration can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingRelayEntryTimeoutNotificationRewardMultiplierUpdateTime

```solidity
function getRemainingRelayEntryTimeoutNotificationRewardMultiplierUpdateTime() external view returns (uint256)
```

Get the time remaining until the relay entry timeout
notification reward multiplier duration can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingUnauthorizedSigningNotificationRewardMultiplierUpdateTime

```solidity
function getRemainingUnauthorizedSigningNotificationRewardMultiplierUpdateTime() external view returns (uint256)
```

Get the time remaining until the unauthorized signing
notification reward multiplier duration can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingDkgMaliciousResultNotificationRewardMultiplierUpdateTime

```solidity
function getRemainingDkgMaliciousResultNotificationRewardMultiplierUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG malicious result
notification reward multiplier duration can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingDkgResultSubmissionGasUpdateTime

```solidity
function getRemainingDkgResultSubmissionGasUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG result submission gas
duration can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingDkgResultApprovalGasOffsetUpdateTime

```solidity
function getRemainingDkgResultApprovalGasOffsetUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG approval gas offset duration
can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingNotifyOperatorInactivityGasOffsetUpdateTime

```solidity
function getRemainingNotifyOperatorInactivityGasOffsetUpdateTime() external view returns (uint256)
```

Get the time remaining until the operator inactivity notification
gas offset duration can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingRelayEntrySubmissionGasOffsetUpdateTime

```solidity
function getRemainingRelayEntrySubmissionGasOffsetUpdateTime() external view returns (uint256)
```

Get the time remaining until the relay entry submission gas offset
duration can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingChangeTime

```solidity
function getRemainingChangeTime(uint256 changeTimestamp) internal view returns (uint256)
```

Gets the time remaining until the governable parameter update
can be committed.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| changeTimestamp | uint256 | Timestamp indicating the beginning of the change. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

## Reimbursable

### reimbursementPool

```solidity
contract ReimbursementPool reimbursementPool
```

### ReimbursementPoolUpdated

```solidity
event ReimbursementPoolUpdated(address newReimbursementPool)
```

### refundable

```solidity
modifier refundable(address receiver)
```

### onlyReimbursableAdmin

```solidity
modifier onlyReimbursableAdmin()
```

### updateReimbursementPool

```solidity
function updateReimbursementPool(contract ReimbursementPool _reimbursementPool) external
```

## ReimbursementPool

### isAuthorized

```solidity
mapping(address => bool) isAuthorized
```

Authorized contracts that can interact with the reimbursment pool.
Authorization can be granted and removed by the owner.

### staticGas

```solidity
uint256 staticGas
```

Static gas includes:
- cost of the refund function
- base transaction cost

### maxGasPrice

```solidity
uint256 maxGasPrice
```

Max gas price used to reimburse a transaction submitter. Protects
against malicious operator-miners.

### StaticGasUpdated

```solidity
event StaticGasUpdated(uint256 newStaticGas)
```

### MaxGasPriceUpdated

```solidity
event MaxGasPriceUpdated(uint256 newMaxGasPrice)
```

### SendingEtherFailed

```solidity
event SendingEtherFailed(uint256 refundAmount, address receiver)
```

### AuthorizedContract

```solidity
event AuthorizedContract(address thirdPartyContract)
```

### UnauthorizedContract

```solidity
event UnauthorizedContract(address thirdPartyContract)
```

### FundsWithdrawn

```solidity
event FundsWithdrawn(uint256 withdrawnAmount, address receiver)
```

### constructor

```solidity
constructor(uint256 _staticGas, uint256 _maxGasPrice) public
```

### receive

```solidity
receive() external payable
```

Receive ETH

### refund

```solidity
function refund(uint256 gasSpent, address receiver) external
```

Refunds ETH to a spender for executing specific transactions.

Ignoring the result of sending ETH to a receiver is made on purpose.
For EOA receiving ETH should always work. If a receiver is a smart
contract, then we do not want to fail a transaction, because in some
cases the refund is done at the very end of multiple calls where all
the previous calls were already paid off. It is a receiver's smart
contract resposibility to make sure it can receive ETH.
Only authorized contracts are allowed calling this function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| gasSpent | uint256 | Gas spent on a transaction that needs to be reimbursed. |
| receiver | address | Address where the reimbursment is sent. |

### authorize

```solidity
function authorize(address _contract) external
```

Authorize a contract that can interact with this reimbursment pool.
Can be authorized by the owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _contract | address | Authorized contract. |

### unauthorize

```solidity
function unauthorize(address _contract) external
```

Unauthorize a contract that was previously authorized to interact
with this reimbursment pool. Can be unauthorized by the
owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _contract | address | Authorized contract. |

### setStaticGas

```solidity
function setStaticGas(uint256 _staticGas) external
```

Setting a static gas cost for executing a transaction. Can be set
by the owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _staticGas | uint256 | Static gas cost. |

### setMaxGasPrice

```solidity
function setMaxGasPrice(uint256 _maxGasPrice) external
```

Setting a max gas price for transactions. Can be set by the
owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _maxGasPrice | uint256 | Max gas price used to reimburse tx submitters. |

### withdrawAll

```solidity
function withdrawAll(address receiver) external
```

Withdraws all ETH from this pool which are sent to a given
address. Can be set by the owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| receiver | address | An address where ETH is sent. |

### withdraw

```solidity
function withdraw(uint256 amount, address receiver) public
```

Withdraws ETH amount from this pool which are sent to a given
address. Can be set by the owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | Amount to withdraw from the pool. |
| receiver | address | An address where ETH is sent. |

## IRandomBeacon

### requestRelayEntry

```solidity
function requestRelayEntry(contract IRandomBeaconConsumer callbackContract) external
```

Creates a request to generate a new relay entry. Requires a
request fee denominated in T token.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| callbackContract | contract IRandomBeaconConsumer | Beacon consumer callback contract. |

## IRandomBeaconConsumer

### __beaconCallback

```solidity
function __beaconCallback(uint256 relayEntry, uint256 blockNumber) external
```

Receives relay entry produced by Keep Random Beacon. This function
should be called only by Keep Random Beacon.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| relayEntry | uint256 | Relay entry (random number) produced by Keep Random Beacon. |
| blockNumber | uint256 | Block number at which the relay entry was submitted to the chain. |

## AltBn128

Implementations of common elliptic curve operations on Ethereum's
(poorly named) alt_bn128 curve. Whenever possible, use post-Byzantium
pre-compiled contracts to offset gas costs. Note that these
pre-compiles might not be available on all (eg private) chains.

### G1Point

```solidity
struct G1Point {
  uint256 x;
  uint256 y;
}
```

### gfP2

```solidity
struct gfP2 {
  uint256 x;
  uint256 y;
}
```

### G2Point

```solidity
struct G2Point {
  struct AltBn128.gfP2 x;
  struct AltBn128.gfP2 y;
}
```

### p

```solidity
uint256 p
```

### g1x

```solidity
uint256 g1x
```

Gets generator of G1 group.
Taken from go-ethereum/crypto/bn256/cloudflare/curve.go

### g1y

```solidity
uint256 g1y
```

### g2xx

```solidity
uint256 g2xx
```

Gets generator of G2 group.
Taken from go-ethereum/crypto/bn256/cloudflare/twist.go

### g2xy

```solidity
uint256 g2xy
```

### g2yx

```solidity
uint256 g2yx
```

### g2yy

```solidity
uint256 g2yy
```

### twistBx

```solidity
uint256 twistBx
```

Gets twist curve B constant.
Taken from go-ethereum/crypto/bn256/cloudflare/twist.go

### twistBy

```solidity
uint256 twistBy
```

### hexRootX

```solidity
uint256 hexRootX
```

Gets root of the point where x and y are equal.

### hexRootY

```solidity
uint256 hexRootY
```

### g1YFromX

```solidity
function g1YFromX(uint256 x) internal view returns (uint256)
```

g1YFromX computes a Y value for a G1 point based on an X value.
This computation is simply evaluating the curve equation for Y on a
given X, and allows a point on the curve to be represented by just
an X value + a sign bit.

### g1HashToPoint

```solidity
function g1HashToPoint(bytes m) internal view returns (struct AltBn128.G1Point)
```

Hash a byte array message, m, and map it deterministically to a
point on G1. Note that this approach was chosen for its simplicity
and lower gas cost on the EVM, rather than good distribution of
points on G1.

### g1Decompress

```solidity
function g1Decompress(bytes32 m) internal view returns (struct AltBn128.G1Point)
```

Decompress a point on G1 from a single uint256.

### g1Add

```solidity
function g1Add(struct AltBn128.G1Point a, struct AltBn128.G1Point b) internal view returns (struct AltBn128.G1Point c)
```

Wraps the point addition pre-compile introduced in Byzantium.
Returns the sum of two points on G1. Revert if the provided points
are not on the curve.

### isG1PointOnCurve

```solidity
function isG1PointOnCurve(struct AltBn128.G1Point point) internal view returns (bool)
```

Returns true if G1 point is on the curve.

### scalarMultiply

```solidity
function scalarMultiply(struct AltBn128.G1Point p_1, uint256 scalar) internal view returns (struct AltBn128.G1Point p_2)
```

Wraps the scalar point multiplication pre-compile introduced in
Byzantium. The result of a point from G1 multiplied by a scalar
should match the point added to itself the same number of times.
Revert if the provided point isn't on the curve.

### pairing

```solidity
function pairing(struct AltBn128.G1Point p1, struct AltBn128.G2Point p2, struct AltBn128.G1Point p3, struct AltBn128.G2Point p4) internal view returns (bool result)
```

Wraps the pairing check pre-compile introduced in Byzantium.
Returns the result of a pairing check of 2 pairs
(G1 p1, G2 p2) (G1 p3, G2 p4)

### getP

```solidity
function getP() internal pure returns (uint256)
```

### g1

```solidity
function g1() internal pure returns (struct AltBn128.G1Point)
```

### g2

```solidity
function g2() internal pure returns (struct AltBn128.G2Point)
```

### g2YFromX

```solidity
function g2YFromX(struct AltBn128.gfP2 _x) internal pure returns (struct AltBn128.gfP2 y)
```

g2YFromX computes a Y value for a G2 point based on an X value.
This computation is simply evaluating the curve equation for Y on a
given X, and allows a point on the curve to be represented by just
an X value + a sign bit.

### g1Compress

```solidity
function g1Compress(struct AltBn128.G1Point point) internal pure returns (bytes32)
```

Compress a point on G1 to a single uint256 for serialization.

### g2Compress

```solidity
function g2Compress(struct AltBn128.G2Point point) internal pure returns (bytes)
```

Compress a point on G2 to a pair of uint256 for serialization.

### g1Unmarshal

```solidity
function g1Unmarshal(bytes m) internal pure returns (struct AltBn128.G1Point)
```

Unmarshals a point on G1 from bytes in an uncompressed form.

### g1Marshal

```solidity
function g1Marshal(struct AltBn128.G1Point point) internal pure returns (bytes)
```

Marshals a point on G1 to bytes form.

### g2Unmarshal

```solidity
function g2Unmarshal(bytes m) internal pure returns (struct AltBn128.G2Point)
```

Unmarshals a point on G2 from bytes in an uncompressed form.

### g2Decompress

```solidity
function g2Decompress(bytes m) internal pure returns (struct AltBn128.G2Point)
```

Decompress a point on G2 from a pair of uint256.

### gfP2Add

```solidity
function gfP2Add(struct AltBn128.gfP2 a, struct AltBn128.gfP2 b) internal pure returns (struct AltBn128.gfP2)
```

Returns the sum of two gfP2 field elements.

### gfP2Multiply

```solidity
function gfP2Multiply(struct AltBn128.gfP2 a, struct AltBn128.gfP2 b) internal pure returns (struct AltBn128.gfP2)
```

Returns multiplication of two gfP2 field elements.

### gfP2Pow

```solidity
function gfP2Pow(struct AltBn128.gfP2 _a, uint256 _exp) internal pure returns (struct AltBn128.gfP2 result)
```

Returns gfP2 element to the power of the provided exponent.

### gfP2Square

```solidity
function gfP2Square(struct AltBn128.gfP2 a) internal pure returns (struct AltBn128.gfP2)
```

### gfP2Cube

```solidity
function gfP2Cube(struct AltBn128.gfP2 a) internal pure returns (struct AltBn128.gfP2)
```

### gfP2CubeAddTwistB

```solidity
function gfP2CubeAddTwistB(struct AltBn128.gfP2 a) internal pure returns (struct AltBn128.gfP2)
```

### g2X2y

```solidity
function g2X2y(struct AltBn128.gfP2 x, struct AltBn128.gfP2 y) internal pure returns (bool)
```

Returns true if G2 point's y^2 equals x.

### isG2PointOnCurve

```solidity
function isG2PointOnCurve(struct AltBn128.G2Point point) internal pure returns (bool)
```

Returns true if G2 point is on the curve.

## BLS

Library for verification of 2-pairing-check BLS signatures, including
basic, aggregated, or reconstructed threshold BLS signatures, generated
using the AltBn128 curve.

### sign

```solidity
function sign(bytes message, uint256 secretKey) external view returns (bytes)
```

Creates a signature over message using the provided secret key.

### verifyBytes

```solidity
function verifyBytes(bytes publicKey, bytes message, bytes signature) external view returns (bool)
```

Wraps the functionality of BLS.verify, but hashes a message to
a point on G1 and marshal to bytes first to allow raw bytes
verification.

### verify

```solidity
function verify(bytes publicKey, bytes message, bytes signature) public view returns (bool)
```

Verify performs the pairing operation to check if the signature
is correct for the provided message and the corresponding public
key. Public key must be a valid point on G2 curve in an
uncompressed format. Message must be a valid point on G1 curve in
an uncompressed format. Signature must be a valid point on G1
curve in an uncompressed format.

### _verify

```solidity
function _verify(struct AltBn128.G2Point publicKey, struct AltBn128.G1Point message, struct AltBn128.G1Point signature) public view returns (bool)
```

## BeaconAuthorization

Library managing the state of stake authorizations for the operator
contract and the presence of operators in the sortition
pool based on the stake authorized for them.

### Parameters

```solidity
struct Parameters {
  uint96 minimumAuthorization;
  uint64 authorizationDecreaseDelay;
  uint64 authorizationDecreaseChangePeriod;
}
```

### AuthorizationDecrease

```solidity
struct AuthorizationDecrease {
  uint96 decreasingBy;
  uint64 decreasingAt;
}
```

### Data

```solidity
struct Data {
  struct BeaconAuthorization.Parameters parameters;
  mapping(address => address) stakingProviderToOperator;
  mapping(address => address) operatorToStakingProvider;
  mapping(address => struct BeaconAuthorization.AuthorizationDecrease) pendingDecreases;
}
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

### setParameters

```solidity
function setParameters(struct BeaconAuthorization.Data self, uint96 _minimumAuthorization, uint64 _authorizationDecreaseDelay, uint64 _authorizationDecreaseChangePeriod) external
```

Updates authorization-related parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BeaconAuthorization.Data |  |
| _minimumAuthorization | uint96 | New value of the minimum authorization for the beacon. Without at least the minimum authorization, staking provider is not eligible to join and operate in the network. |
| _authorizationDecreaseDelay | uint64 | New value of the authorization decrease delay. It is the time in seconds that needs to pass between the time authorization decrease is requested and the time the authorization decrease can be approved, no matter the authorization decrease amount. |
| _authorizationDecreaseChangePeriod | uint64 | New value of the authorization decrease change period. It is the time in seconds, before authorization decrease delay end, during which the pending authorization decrease request can be overwritten. If set to 0, pending authorization decrease request can not be overwritten until the entire `authorizationDecreaseDelay` ends. If set to value equal `authorizationDecreaseDelay`, request can always be overwritten. |

### registerOperator

```solidity
function registerOperator(struct BeaconAuthorization.Data self, address operator) public
```

Used by staking provider to set operator address that will
operate a node. The given staking provider can set operator
address only one time. The operator address can not be changed
and must be unique. Reverts if the operator is already set for
the staking provider or if the operator address is already in
use. Reverts if there is a pending authorization decrease for
the staking provider.

### authorizationIncreased

```solidity
function authorizationIncreased(struct BeaconAuthorization.Data self, address stakingProvider, uint96 fromAmount, uint96 toAmount) external
```

Used by T staking contract to inform the beacon that the
authorized stake amount for the given staking provider increased.

Reverts if the authorization amount is below the minimum.

The function is not updating the sortition pool. Sortition pool
state needs to be updated by the operator with a call to
`joinSortitionPool` or `updateOperatorStatus`.

Should only be callable by T staking contract.

### authorizationDecreaseRequested

```solidity
function authorizationDecreaseRequested(struct BeaconAuthorization.Data self, address stakingProvider, uint96 fromAmount, uint96 toAmount) public
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

Should only be callable by T staking contract.

### approveAuthorizationDecrease

```solidity
function approveAuthorizationDecrease(struct BeaconAuthorization.Data self, contract IStaking tokenStaking, address stakingProvider) public
```

Approves the previously registered authorization decrease
request. Reverts if authorization decrease delay have not passed
yet or if the authorization decrease was not requested for the
given staking provider.

### involuntaryAuthorizationDecrease

```solidity
function involuntaryAuthorizationDecrease(struct BeaconAuthorization.Data self, contract IStaking tokenStaking, contract SortitionPool sortitionPool, address stakingProvider, uint96 fromAmount, uint96 toAmount) external
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

Should only be callable by T staking contract.

### joinSortitionPool

```solidity
function joinSortitionPool(struct BeaconAuthorization.Data self, contract IStaking tokenStaking, contract SortitionPool sortitionPool) public
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
function updateOperatorStatus(struct BeaconAuthorization.Data self, contract IStaking tokenStaking, contract SortitionPool sortitionPool, address operator) public
```

Updates status of the operator in the sortition pool. If there
was an authorization decrease requested, it is activated by
starting the authorization decrease delay.
Function reverts if the operator is not known.

### isOperatorUpToDate

```solidity
function isOperatorUpToDate(struct BeaconAuthorization.Data self, contract IStaking tokenStaking, contract SortitionPool sortitionPool, address operator) external view returns (bool)
```

Checks if the operator's authorized stake is in sync with
operator's weight in the sortition pool.
If the operator is not in the sortition pool and their
authorized stake is non-zero, function returns false.

### eligibleStake

```solidity
function eligibleStake(struct BeaconAuthorization.Data self, contract IStaking tokenStaking, address stakingProvider) external view returns (uint96)
```

Returns the current value of the staking provider's eligible
stake. Eligible stake is defined as the currently authorized
stake minus the pending authorization decrease. Eligible stake
is what is used for operator's weight in the pool. If the
authorized stake minus the pending authorization decrease is
below the minimum authorization, eligible stake is 0.

This function can be exposed to the public in contrast to the
second variant accepting `decreasingBy` as a parameter.

### eligibleStake

```solidity
function eligibleStake(struct BeaconAuthorization.Data self, contract IStaking tokenStaking, address stakingProvider, uint96 decreasingBy) public view returns (uint96)
```

Returns the current value of the staking provider's eligible
stake. Eligible stake is defined as the currently authorized
stake minus the pending authorization decrease. Eligible stake
is what is used for operator's weight in the pool. If the
authorized stake minus the pending authorization decrease is
below the minimum authorization, eligible stake is 0.

This function is not intended to be exposes to the public.
`decreasingBy` must be fetched from `pendingDecreases` mapping and
it is passed as a parameter to optimize gas usage of functions that
call `eligibleStake` and need to use `AuthorizationDecrease`
fetched from `pendingDecreases` for some additional logic.

### pendingAuthorizationDecrease

```solidity
function pendingAuthorizationDecrease(struct BeaconAuthorization.Data self, address stakingProvider) public view returns (uint96)
```

Returns the amount of stake that is pending authorization
decrease for the given staking provider. If no authorization
decrease has been requested, returns zero.

### remainingAuthorizationDecreaseDelay

```solidity
function remainingAuthorizationDecreaseDelay(struct BeaconAuthorization.Data self, address stakingProvider) external view returns (uint64)
```

Returns the remaining time in seconds that needs to pass before
the requested authorization decrease can be approved.
If the sortition pool state was not updated yet by the operator
after requesting the authorization decrease, returns
`type(uint64).max`.

## BeaconDkg

### Parameters

```solidity
struct Parameters {
  uint256 resultChallengePeriodLength;
  uint256 resultChallengeExtraGas;
  uint256 resultSubmissionTimeout;
  uint256 submitterPrecedencePeriodLength;
}
```

### Data

```solidity
struct Data {
  contract SortitionPool sortitionPool;
  contract BeaconDkgValidator dkgValidator;
  struct BeaconDkg.Parameters parameters;
  uint256 startBlock;
  uint256 seed;
  uint256 resultSubmissionStartBlockOffset;
  bytes32 submittedResultHash;
  uint256 submittedResultBlock;
}
```

### Result

```solidity
struct Result {
  uint256 submitterMemberIndex;
  bytes groupPubKey;
  uint8[] misbehavedMembersIndices;
  bytes signatures;
  uint256[] signingMembersIndices;
  uint32[] members;
  bytes32 membersHash;
}
```

### State

```solidity
enum State {
  IDLE,
  AWAITING_SEED,
  KEY_GENERATION,
  AWAITING_RESULT,
  CHALLENGE
}
```

### groupSize

```solidity
uint256 groupSize
```

Size of a group in the threshold relay.

### offchainDkgTime

```solidity
uint256 offchainDkgTime
```

Time in blocks after which DKG result is complete and ready to be

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

### DkgStateLocked

```solidity
event DkgStateLocked()
```

### DkgSeedTimedOut

```solidity
event DkgSeedTimedOut()
```

### init

```solidity
function init(struct BeaconDkg.Data self, contract SortitionPool _sortitionPool, contract BeaconDkgValidator _dkgValidator) internal
```

Initializes SortitionPool and DKGValidator addresses.
Can be performed only once.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BeaconDkg.Data |  |
| _sortitionPool | contract SortitionPool | Sortition Pool reference |
| _dkgValidator | contract BeaconDkgValidator | DKGValidator reference |

### currentState

```solidity
function currentState(struct BeaconDkg.Data self) internal view returns (enum BeaconDkg.State state)
```

Determines the current state of group creation. It doesn't take
timeouts into consideration. The timeouts should be tracked and
notified separately.

### lockState

```solidity
function lockState(struct BeaconDkg.Data self) internal
```

Locks the sortition pool and starts awaiting for the
group creation seed.

### start

```solidity
function start(struct BeaconDkg.Data self, uint256 seed) internal
```

### submitResult

```solidity
function submitResult(struct BeaconDkg.Data self, struct BeaconDkg.Result result) external
```

Allows to submit a DKG result. The submitted result does not go
through a validation and before it gets accepted, it needs to
wait through the challenge period during which everyone has
a chance to challenge the result as invalid one. Submitter of
the result needs to be in the sortition pool and if the result
gets challenged, the submitter will get slashed.

### hasDkgTimedOut

```solidity
function hasDkgTimedOut(struct BeaconDkg.Data self) internal view returns (bool)
```

Checks if DKG timed out. The DKG timeout period includes time required
for off-chain protocol execution and time for the result publication.
After this time a result cannot be submitted and DKG can be notified
about the timeout. DKG period is adjusted by result submission
offset that include blocks that were mined while invalid result
has been registered until it got challenged.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if DKG timed out, false otherwise. |

### notifyTimeout

```solidity
function notifyTimeout(struct BeaconDkg.Data self) internal
```

Notifies about DKG timeout.

### notifySeedTimedOut

```solidity
function notifySeedTimedOut(struct BeaconDkg.Data self) internal
```

Notifies about the seed was not delivered and restores the
initial DKG state (IDLE).

### approveResult

```solidity
function approveResult(struct BeaconDkg.Data self, struct BeaconDkg.Result result) external returns (uint32[] misbehavedMembers)
```

Approves DKG result. Can be called when the challenge period for
the submitted result is finished. Considers the submitted result
as valid. For the first `submitterPrecedencePeriodLength`
blocks after the end of the challenge period can be called only
by the DKG result submitter. After that time, can be called by
anyone.

Can be called after a challenge period for the submitted result.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BeaconDkg.Data |  |
| result | struct BeaconDkg.Result | Result to approve. Must match the submitted result stored during `submitResult`. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| misbehavedMembers | uint32[] | Identifiers of members who misbehaved during DKG. |

### challengeResult

```solidity
function challengeResult(struct BeaconDkg.Data self, struct BeaconDkg.Result result) external returns (bytes32 maliciousResultHash, uint32 maliciousSubmitter)
```

Challenges DKG result. If the submitted result is proved to be
invalid it reverts the DKG back to the result submission phase.

Can be called during a challenge period for the submitted result.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BeaconDkg.Data |  |
| result | struct BeaconDkg.Result | Result to challenge. Must match the submitted result stored during `submitResult`. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| maliciousResultHash | bytes32 | Hash of the malicious result. |
| maliciousSubmitter | uint32 | Identifier of the malicious submitter. |

### requireChallengeExtraGas

```solidity
function requireChallengeExtraGas(struct BeaconDkg.Data self) internal view
```

Due to EIP150, 1/64 of the gas is not forwarded to the call, and
will be kept to execute the remaining operations in the function
after the call inside the try-catch.

To ensure there is no way for the caller to manipulate gas limit
in such a way that the call inside try-catch fails with out-of-gas
and the rest of the function is executed with the remaining
1/64 of gas, we require an extra gas amount to be left at the
end of the call to the function challenging DKG result and
wrapping the call to BeaconDkgValidator and TokenStaking
contracts inside a try-catch.

### setParameters

```solidity
function setParameters(struct BeaconDkg.Data self, uint256 _resultChallengePeriodLength, uint256 _resultChallengeExtraGas, uint256 _resultSubmissionTimeout, uint256 _submitterPrecedencePeriodLength) internal
```

Updates DKG-related parameters

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BeaconDkg.Data |  |
| _resultChallengePeriodLength | uint256 | New value of the result challenge period length. It is the number of blocks for which a DKG result can be challenged. |
| _resultChallengeExtraGas | uint256 | New value of the result challenge extra gas. It is the extra gas required to be left at the end of the challenge DKG result transaction. |
| _resultSubmissionTimeout | uint256 | New value of the result submission timeout in seconds. It is a timeout for a group to provide a DKG result. |
| _submitterPrecedencePeriodLength | uint256 | New value of the submitter precedence period length in blocks. It is the time during which only the original DKG result submitter can approve it. |

### complete

```solidity
function complete(struct BeaconDkg.Data self) internal
```

Completes DKG by cleaning up state.

Should be called after DKG times out or a result is approved.

## BeaconInactivity

### Claim

```solidity
struct Claim {
  uint64 groupId;
  uint256[] inactiveMembersIndices;
  bytes signatures;
  uint256[] signingMembersIndices;
}
```

### groupThreshold

```solidity
uint256 groupThreshold
```

The minimum number of group members needed to interact according
to the protocol to produce a valid inactivity claim.

### signatureByteSize

```solidity
uint256 signatureByteSize
```

Size in bytes of a single signature produced by member
supporting the inactivity claim.

### verifyClaim

```solidity
function verifyClaim(contract SortitionPool sortitionPool, struct BeaconInactivity.Claim claim, bytes groupPubKey, uint256 nonce, uint32[] groupMembers) external view returns (uint32[] inactiveMembers)
```

Verifies the inactivity claim according to the rules defined in
`Claim` struct documentation. Reverts if verification fails.

Group members hash is validated upstream in
RandomBeacon.notifyOperatorInactivity()

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| sortitionPool | contract SortitionPool | Sortition pool reference |
| claim | struct BeaconInactivity.Claim | Inactivity claim |
| groupPubKey | bytes | Public key of the group raising the claim |
| nonce | uint256 | Current nonce for group used in the claim |
| groupMembers | uint32[] | Identifiers of group members |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| inactiveMembers | uint32[] | Identifiers of members who are inactive |

### validateMembersIndices

```solidity
function validateMembersIndices(uint256[] indices, uint256 groupSize) internal pure
```

Validates members indices array. Array is considered valid
if its size and each single index are in [1, groupSize] range,
indexes are unique, and sorted in an ascending order.
Reverts if validation fails.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| indices | uint256[] | Array to validate |
| groupSize | uint256 | Group size used as reference |

## BytesLib

### concatStorage

```solidity
function concatStorage(bytes _preBytes, bytes _postBytes) internal
```

### equalStorage

```solidity
function equalStorage(bytes _preBytes, bytes _postBytes) internal view returns (bool)
```

### concat

```solidity
function concat(bytes _preBytes, bytes _postBytes) internal pure returns (bytes)
```

### slice

```solidity
function slice(bytes _bytes, uint256 _start, uint256 _length) internal pure returns (bytes res)
```

### toAddress

```solidity
function toAddress(bytes _bytes, uint256 _start) internal pure returns (address)
```

### toUint8

```solidity
function toUint8(bytes _bytes, uint256 _start) internal pure returns (uint8)
```

### toUint

```solidity
function toUint(bytes _bytes, uint256 _start) internal pure returns (uint256)
```

### equal

```solidity
function equal(bytes _preBytes, bytes _postBytes) internal pure returns (bool)
```

### toBytes32

```solidity
function toBytes32(bytes _source) internal pure returns (bytes32 result)
```

### keccak256Slice

```solidity
function keccak256Slice(bytes _bytes, uint256 _start, uint256 _length) internal pure returns (bytes32 result)
```

## Callback

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

## Groups

This library is used as a registry of created groups.

This library should be used along with DKG library that ensures linear
groups creation (only one group creation happens at a time). A candidate
group has to be popped or activated before adding a new candidate group.

### Group

```solidity
struct Group {
  bytes groupPubKey;
  uint256 registrationBlockNumber;
  bytes32 membersHash;
  bool terminated;
}
```

### Data

```solidity
struct Data {
  mapping(bytes32 => struct Groups.Group) groupsData;
  bytes32[] groupsRegistry;
  uint64[] activeTerminatedGroups;
  uint64 expiredGroupOffset;
  uint256 groupLifetime;
}
```

### GroupRegistered

```solidity
event GroupRegistered(uint64 groupId, bytes groupPubKey)
```

### validatePublicKey

```solidity
function validatePublicKey(struct Groups.Data self, bytes groupPubKey) internal view
```

Performs preliminary validation of a new group public key.
The group public key must be unique and have 128 bytes in length.
If the validation fails, the function reverts. This function
must be called first for a public key of a group added with
`addGroup` function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Groups.Data |  |
| groupPubKey | bytes | Candidate group public key |

### addGroup

```solidity
function addGroup(struct Groups.Data self, bytes groupPubKey, bytes32 membersHash) internal
```

Adds a new candidate group. The group is stored with group public
key and group members, but is not yet activated.

The group members list is stored with all misbehaved members filtered out.
The code calling this function should ensure that the number of
candidate (not activated) groups is never more than one.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Groups.Data |  |
| groupPubKey | bytes | Generated candidate group public key |
| membersHash | bytes32 | Keccak256 hash of members that actively took part in DKG. |

### expireOldGroups

```solidity
function expireOldGroups(struct Groups.Data self) internal
```

Goes through groups starting from the oldest one that is still
active and checks if it hasn't expired. If so, updates the information
about expired groups so that all expired groups are marked as such.

### terminateGroup

```solidity
function terminateGroup(struct Groups.Data self, uint64 groupId) internal
```

Terminates group with the provided index. Reverts if the group
is already terminated.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Groups.Data |  |
| groupId | uint64 | Index in the groupRegistry array. |

### selectGroup

```solidity
function selectGroup(struct Groups.Data self, uint256 seed) internal returns (uint64)
```

Returns an index of a randomly selected active group. Terminated
and expired groups are not considered as active.
Before new group is selected, information about expired groups
is updated. At least one active group needs to be present for this
function to succeed.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Groups.Data |  |
| seed | uint256 | Random number used as a group selection seed. |

### setGroupLifetime

```solidity
function setGroupLifetime(struct Groups.Data self, uint256 lifetime) internal
```

Setter for group lifetime.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Groups.Data |  |
| lifetime | uint256 | Lifetime of a group in blocks. |

### isGroupTerminated

```solidity
function isGroupTerminated(struct Groups.Data self, uint64 groupId) internal view returns (bool)
```

Checks if group with the given index is terminated.

### groupLifetimeOf

```solidity
function groupLifetimeOf(struct Groups.Data self, bytes32 groupPubKeyHash) internal view returns (uint256)
```

Gets the cutoff time until which the given group is considered
to be active assuming it hasn't been terminated before.

### isGroupActive

```solidity
function isGroupActive(struct Groups.Data self, uint64 groupId) internal view returns (bool)
```

Checks if group with the given index is active and non-terminated.

### getGroup

```solidity
function getGroup(struct Groups.Data self, uint64 groupId) internal view returns (struct Groups.Group)
```

### getGroup

```solidity
function getGroup(struct Groups.Data self, bytes groupPubKey) internal view returns (struct Groups.Group)
```

### numberOfActiveGroups

```solidity
function numberOfActiveGroups(struct Groups.Data self) internal view returns (uint64)
```

Gets the number of active groups. Expired and terminated
groups are not counted as active.

### shiftByExpiredGroups

```solidity
function shiftByExpiredGroups(struct Groups.Data self, uint64 selectedIndex) internal view returns (uint64)
```

Evaluates the shift of a selected group index based on the number
of expired groups.

### shiftByTerminatedGroups

```solidity
function shiftByTerminatedGroups(struct Groups.Data self, uint64 selectedIndex) internal view returns (uint64)
```

Evaluates the shift of a selected group index based on the number
of non-expired but terminated groups.

## ModUtils

### modExp

```solidity
function modExp(uint256 base, uint256 exponent, uint256 p) internal view returns (uint256 o)
```

Wraps the modular exponent pre-compile introduced in Byzantium.
Returns base^exponent mod p.

### modSqrt

```solidity
function modSqrt(uint256 a, uint256 p) internal view returns (uint256)
```

Calculates and returns the square root of a mod p if such a square
root exists. The modulus p must be an odd prime. If a square root
does not exist, function returns 0.

### legendre

```solidity
function legendre(uint256 a, uint256 p) internal view returns (int256)
```

Calculates the Legendre symbol of the given a mod p.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | int256 | Returns 1 if a is a quadratic residue mod p, -1 if it is a non-quadratic residue, and 0 if a is 0. |

## Relay

### Data

```solidity
struct Data {
  uint64 requestCount;
  uint64 currentRequestID;
  uint64 currentRequestGroupID;
  uint64 currentRequestStartBlock;
  struct AltBn128.G1Point previousEntry;
  uint32 relayEntrySoftTimeout;
  uint32 relayEntryHardTimeout;
  uint96 relayEntrySubmissionFailureSlashingAmount;
}
```

### relaySeed

```solidity
bytes relaySeed
```

Seed used as the first relay entry value.
It's a G1 point G * PI =
G * 31415926535897932384626433832795028841971693993751058209749445923078164062862
Where G is the generator of G1 abstract cyclic group.

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

### initSeedEntry

```solidity
function initSeedEntry(struct Relay.Data self) internal
```

Initializes the very first `previousEntry` with an initial
`relaySeed` value. Can be performed only once.

### requestEntry

```solidity
function requestEntry(struct Relay.Data self, uint64 groupId) internal
```

Creates a request to generate a new relay entry, which will
include a random number (by signing the previous entry's
random number).

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Relay.Data |  |
| groupId | uint64 | Identifier of the group chosen to handle the request. |

### submitEntryBeforeSoftTimeout

```solidity
function submitEntryBeforeSoftTimeout(struct Relay.Data self, bytes entry, bytes groupPubKey) internal
```

Creates a new relay entry. Gas-optimized version that can be
called only before the soft timeout. This should be the majority
of cases.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Relay.Data |  |
| entry | bytes | Group BLS signature over the previous entry. |
| groupPubKey | bytes | Public key of the group which signed the relay entry. |

### submitEntry

```solidity
function submitEntry(struct Relay.Data self, bytes entry, bytes groupPubKey) internal returns (uint96)
```

Creates a new relay entry. Can be called at any time.
In case the soft timeout has not been exceeded, it is more
gas-efficient to call the second variation of `submitEntry`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Relay.Data |  |
| entry | bytes | Group BLS signature over the previous entry. |
| groupPubKey | bytes | Public key of the group which signed the relay entry. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint96 | slashingAmount Amount by which group members should be slashed in case the relay entry was submitted after the soft timeout. The value is zero if entry was submitted on time. |

### _submitEntry

```solidity
function _submitEntry(struct Relay.Data self, bytes entry, bytes groupPubKey) internal
```

### calculateSlashingAmount

```solidity
function calculateSlashingAmount(struct Relay.Data self) internal view returns (uint96)
```

Calculates the slashing amount for all group members.

Must be used when a soft timeout was hit.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint96 | Amount by which group members should be slashed in case the relay entry was submitted after the soft timeout. |

### setTimeouts

```solidity
function setTimeouts(struct Relay.Data self, uint256 _relayEntrySoftTimeout, uint256 _relayEntryHardTimeout) internal
```

Updates relay-related parameters

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Relay.Data |  |
| _relayEntrySoftTimeout | uint256 | New relay entry soft timeout value. It is the time in blocks during which a result is expected to be submitted so that the group is not slashed. |
| _relayEntryHardTimeout | uint256 | New relay entry hard timeout value. It is the time in blocks for a group to submit the relay entry before slashing for the full slashing amount happens. |

### setRelayEntrySubmissionFailureSlashingAmount

```solidity
function setRelayEntrySubmissionFailureSlashingAmount(struct Relay.Data self, uint96 newRelayEntrySubmissionFailureSlashingAmount) internal
```

Set relayEntrySubmissionFailureSlashingAmount parameter.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Relay.Data |  |
| newRelayEntrySubmissionFailureSlashingAmount | uint96 | New value of the parameter. |

### retryOnEntryTimeout

```solidity
function retryOnEntryTimeout(struct Relay.Data self, uint64 newGroupId) internal
```

Retries the current relay request in case a relay entry
timeout was reported.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Relay.Data |  |
| newGroupId | uint64 | ID of the group chosen to retry the current request. |

### cleanupOnEntryTimeout

```solidity
function cleanupOnEntryTimeout(struct Relay.Data self) internal
```

Cleans up the current relay request in case a relay entry
timeout was reported.

### isRequestInProgress

```solidity
function isRequestInProgress(struct Relay.Data self) internal view returns (bool)
```

Returns whether a relay entry request is currently in progress.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if there is a request in progress. False otherwise. |

### hasRequestTimedOut

```solidity
function hasRequestTimedOut(struct Relay.Data self) internal view returns (bool)
```

Returns whether the current relay request has timed out.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if the request timed out. False otherwise. |

### softTimeoutBlock

```solidity
function softTimeoutBlock(struct Relay.Data self) internal view returns (uint256)
```

Calculates soft timeout block for the pending relay request.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | The soft timeout block |

