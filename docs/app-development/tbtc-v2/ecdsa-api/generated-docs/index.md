# Solidity API
## Table Of Contents

<!-- toc -->

- [EcdsaDkgValidator](#ecdsadkgvalidator)
- [WalletRegistry](#walletregistry)
- [WalletRegistryGovernance](#walletregistrygovernance)
- [IWalletOwner](#iwalletowner)
- [IWalletRegistry](#iwalletregistry)
- [EcdsaAuthorization](#ecdsaauthorization)
- [EcdsaDkg](#ecdsadkg)
- [EcdsaInactivity](#ecdsainactivity)
- [Wallets](#wallets)

<!-- tocstop -->

## EcdsaDkgValidator

EcdsaDkgValidator allows performing a full validation of DKG result,
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

Size of a group in DKG.

### groupThreshold

```solidity
uint256 groupThreshold
```

The minimum number of group members needed to interact according to
the protocol to produce a signature. The adversary can not learn
anything about the key as long as it does not break into
groupThreshold+1 of members.

### activeThreshold

```solidity
uint256 activeThreshold
```

The minimum number of active and properly behaving group members
during the DKG needed to accept the result. This number is higher
than `groupThreshold` to keep a safety margin for members becoming
inactive after DKG so that the group can still produce signature.

### publicKeyByteSize

```solidity
uint256 publicKeyByteSize
```

Size in bytes of a public key produced by group members during the
the DKG. The length assumes uncompressed ECDSA public key.

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
function validate(struct EcdsaDkg.Result result, uint256 seed, uint256 startBlock) external view returns (bool isValid, string errorMsg)
```

Performs a full validation of DKG result, including checking the
format of fields in the result, declared selected group members,
and signatures of operators supporting the result.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| result | struct EcdsaDkg.Result |  |
| seed | uint256 | seed used to start the DKG and select group members |
| startBlock | uint256 | DKG start block |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| isValid | bool | true if the result is valid, false otherwise |
| errorMsg | string | validation error message; empty for a valid result |

### validateFields

```solidity
function validateFields(struct EcdsaDkg.Result result) public pure returns (bool isValid, string errorMsg)
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
function validateGroupMembers(struct EcdsaDkg.Result result, uint256 seed) public view returns (bool)
```

Performs validation of group members as declared in DKG
result against group members selected by the sortition pool.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| result | struct EcdsaDkg.Result |  |
| seed | uint256 | seed used to start the DKG and select group members |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | true if group members matches; false otherwise |

### validateSignatures

```solidity
function validateSignatures(struct EcdsaDkg.Result result, uint256 startBlock) public view returns (bool)
```

Performs validation of signatures supplied in DKG result.
Note that this function does not check if addresses which
supplied signatures supporting the result are the ones selected
to the group by sortition pool. This function should be used
together with `validateGroupMembers`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| result | struct EcdsaDkg.Result |  |
| startBlock | uint256 | DKG start block |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | true if group members matches; false otherwise |

### validateMembersHash

```solidity
function validateMembersHash(struct EcdsaDkg.Result result) public pure returns (bool)
```

Performs validation of hashed group members that actively took
part in DKG.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| result | struct EcdsaDkg.Result | DKG result |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | true if calculated result's group members hash matches with the one that is challenged. |

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

### _maliciousDkgResultSlashingAmount

```solidity
uint96 _maliciousDkgResultSlashingAmount
```

Slashing amount for submitting a malicious DKG result. Every
DKG result submitted can be challenged for the time of
`dkg.resultChallengePeriodLength`. If the DKG result submitted
is challenged and proven to be malicious, the operator who
submitted the malicious result is slashed for
`_maliciousDkgResultSlashingAmount`.

### _maliciousDkgResultNotificationRewardMultiplier

```solidity
uint256 _maliciousDkgResultNotificationRewardMultiplier
```

Percentage of the staking contract malicious behavior
notification reward which will be transferred to the notifier
reporting about a malicious DKG result. Notifiers are rewarded
from a notifiers treasury pool. For example, if
notification reward is 1000 and the value of the multiplier is
5, the notifier will receive: 5% of 1000 = 50 per each
operator affected.

### _sortitionPoolRewardsBanDuration

```solidity
uint256 _sortitionPoolRewardsBanDuration
```

Duration of the sortition pool rewards ban imposed on operators
who missed their turn for DKG result submission or who failed
a heartbeat.

### _dkgResultSubmissionGas

```solidity
uint256 _dkgResultSubmissionGas
```

Calculated max gas cost for submitting a DKG result. This will
be refunded as part of the DKG approval process. It is in the
submitter's interest to not skip his priority turn on the approval,
otherwise the refund of the DKG submission will be refunded to
another group member that will call the DKG approve function.

### _dkgResultApprovalGasOffset

```solidity
uint256 _dkgResultApprovalGasOffset
```

Gas that is meant to balance the DKG result approval's overall
cost. It can be updated by the governance based on the current
market conditions.

### _notifyOperatorInactivityGasOffset

```solidity
uint256 _notifyOperatorInactivityGasOffset
```

Gas that is meant to balance the notification of an operator
inactivity. It can be updated by the governance based on the
current market conditions.

### _notifySeedTimeoutGasOffset

```solidity
uint256 _notifySeedTimeoutGasOffset
```

Gas that is meant to balance the notification of a seed for DKG
delivery timeout. It can be updated by the governance based on the
current market conditions.

### _notifyDkgTimeoutNegativeGasOffset

```solidity
uint256 _notifyDkgTimeoutNegativeGasOffset
```

Gas that is meant to balance the notification of a DKG protocol
execution timeout. It can be updated by the governance based on the
current market conditions.

The value is subtracted for the refundable gas calculation, as the
DKG timeout notification transaction recovers some gas when cleaning
up the storage.

### inactivityClaimNonce

```solidity
mapping(bytes32 => uint256) inactivityClaimNonce
```

Stores current operator inactivity claim nonce for the given
wallet signing group. Each claim is made with a unique nonce
which protects against claim replay.

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

Used to initialize immutable variables only, use `initialize` function
for upgradable contract initialization on deployment.

### initialize

```solidity
function initialize(contract EcdsaDkgValidator _ecdsaDkgValidator, contract IRandomBeacon _randomBeacon, contract ReimbursementPool _reimbursementPool) external
```

Initializes upgradable contract on deployment.

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
wallet registry governance contract.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| recipient | address | Recipient of withdrawn rewards. |

### registerOperator

```solidity
function registerOperator(address operator) external
```

Used by staking provider to set operator address that will
operate ECDSA node. The given staking provider can set operator
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
by ECDSA. Function reverts if there is no minimum stake
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

Used by T staking contract to inform the application that the
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

Used by T staking contract to inform the application that the
authorization decrease for the given staking provider has been
requested.

Reverts if the amount after deauthorization would be non-zero
and lower than the minimum authorization.

If the operator is not known (`registerOperator` was not called)
it lets to `approveAuthorizationDecrease` immediatelly. If the
operator is known (`registerOperator` was called), the operator
needs to update state of the sortition pool with a call to
`joinSortitionPool` or `updateOperatorStatus`. After the
sortition pool state is in sync, authorization decrease delay
starts.

After authorization decrease delay passes, authorization
decrease request needs to be approved with a call to
`approveAuthorizationDecrease` function.

If there is a pending authorization decrease request, it is
overwritten.

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

Used by T staking contract to inform the application the
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

### upgradeRandomBeacon

```solidity
function upgradeRandomBeacon(contract IRandomBeacon _randomBeacon) external
```

Updates address of the Random Beacon.

Can be called only by the contract guvnor, which should be the
wallet registry governance contract. The caller is responsible for
validating parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _randomBeacon | contract IRandomBeacon | Random Beacon address. |

### updateWalletOwner

```solidity
function updateWalletOwner(contract IWalletOwner _walletOwner) external
```

Updates the wallet owner.

Can be called only by the contract guvnor, which should be the
wallet registry governance contract. The caller is responsible for
validating parameters. The wallet owner has to implement `IWalletOwner`
interface.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _walletOwner | contract IWalletOwner | New wallet owner address. |

### updateAuthorizationParameters

```solidity
function updateAuthorizationParameters(uint96 _minimumAuthorization, uint64 _authorizationDecreaseDelay, uint64 _authorizationDecreaseChangePeriod) external
```

Updates the values of authorization parameters.

Can be called only by the contract guvnor, which should be the
wallet registry governance contract. The caller is responsible for
validating parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _minimumAuthorization | uint96 | New minimum authorization amount. |
| _authorizationDecreaseDelay | uint64 | New authorization decrease delay in seconds. |
| _authorizationDecreaseChangePeriod | uint64 | New authorization decrease change period in seconds. |

### updateDkgParameters

```solidity
function updateDkgParameters(uint256 _seedTimeout, uint256 _resultChallengePeriodLength, uint256 _resultChallengeExtraGas, uint256 _resultSubmissionTimeout, uint256 _submitterPrecedencePeriodLength) external
```

Updates the values of DKG parameters.

Can be called only by the contract guvnor, which should be the
wallet registry governance contract. The caller is responsible for
validating parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _seedTimeout | uint256 | New seed timeout. |
| _resultChallengePeriodLength | uint256 | New DKG result challenge period length. |
| _resultChallengeExtraGas | uint256 | New extra gas value required to be left at the end of the DKG result challenge transaction. |
| _resultSubmissionTimeout | uint256 | New DKG result submission timeout. |
| _submitterPrecedencePeriodLength | uint256 | New submitter precedence period length. |

### updateRewardParameters

```solidity
function updateRewardParameters(uint256 maliciousDkgResultNotificationRewardMultiplier, uint256 sortitionPoolRewardsBanDuration) external
```

Updates the values of reward parameters.

Can be called only by the contract guvnor, which should be the
wallet registry governance contract. The caller is responsible for
validating parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| maliciousDkgResultNotificationRewardMultiplier | uint256 | New value of the DKG malicious result notification reward multiplier. |
| sortitionPoolRewardsBanDuration | uint256 | New sortition pool rewards ban duration in seconds. |

### updateSlashingParameters

```solidity
function updateSlashingParameters(uint96 maliciousDkgResultSlashingAmount) external
```

Updates the values of slashing parameters.

Can be called only by the contract guvnor, which should be the
wallet registry governance contract. The caller is responsible for
validating parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| maliciousDkgResultSlashingAmount | uint96 | New malicious DKG result slashing amount. |

### updateGasParameters

```solidity
function updateGasParameters(uint256 dkgResultSubmissionGas, uint256 dkgResultApprovalGasOffset, uint256 notifyOperatorInactivityGasOffset, uint256 notifySeedTimeoutGasOffset, uint256 notifyDkgTimeoutNegativeGasOffset) external
```

Updates the values of gas-related parameters.

Can be called only by the contract guvnor, which should be the
wallet registry governance contract. The caller is responsible for
validating parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| dkgResultSubmissionGas | uint256 | New DKG result submission gas. |
| dkgResultApprovalGasOffset | uint256 | New DKG result approval gas offset. |
| notifyOperatorInactivityGasOffset | uint256 | New operator inactivity notification gas offset. |
| notifySeedTimeoutGasOffset | uint256 | New seed for DKG delivery timeout notification gas offset. |
| notifyDkgTimeoutNegativeGasOffset | uint256 | New DKG timeout notification gas offset. |

### requestNewWallet

```solidity
function requestNewWallet() external
```

Requests a new wallet creation.

Can be called only by the owner of wallets.
It locks the DKG and request a new relay entry. It expects
that the DKG process will be started once a new relay entry
gets generated.

### closeWallet

```solidity
function closeWallet(bytes32 walletID) external
```

Closes an existing wallet. Reverts if wallet with the given ID
does not exist or if it has already been closed.

Only a Wallet Owner can call this function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletID | bytes32 | ID of the wallet. |

### __beaconCallback

```solidity
function __beaconCallback(uint256 relayEntry, uint256) external
```

A callback that is executed once a new relay entry gets
generated. It starts the DKG process.

Can be called only by the random beacon contract.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| relayEntry | uint256 | Relay entry. |
|  | uint256 |  |

### submitDkgResult

```solidity
function submitDkgResult(struct EcdsaDkg.Result dkgResult) external
```

Submits result of DKG protocol.
The DKG result consists of result submitting member index,
calculated group public key, bytes array of misbehaved members,
concatenation of signatures from group members, indices of members
corresponding to each signature and the list of group members.
The result is registered optimistically and waits for an approval.
The result can be challenged when it is believed to be incorrect.
The challenge verifies the registered result i.a. it checks if members
list corresponds to the expected set of members determined
by the sortition pool.

The message to be signed by each member is keccak256 hash of the
chain ID, calculated group public key, misbehaved members indices
and DKG start block. The calculated hash should be prefixed with
`\x19Ethereum signed message:\n` before signing, so the message to
sign is:
`\x19Ethereum signed message:\n${keccak256(chainID,groupPubKey,misbehavedIndices,startBlock)}`

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| dkgResult | struct EcdsaDkg.Result | DKG result. |

### approveDkgResult

```solidity
function approveDkgResult(struct EcdsaDkg.Result dkgResult) external
```

Approves DKG result. Can be called when the challenge period for
the submitted result is finished. Considers the submitted result
as valid, bans misbehaved group members from the sortition pool
rewards, and completes the group creation by activating the
candidate group. For the first `resultSubmissionTimeout` blocks
after the end of the challenge period can be called only by the
DKG result submitter. After that time, can be called by anyone.
A new wallet based on the DKG result details.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| dkgResult | struct EcdsaDkg.Result | Result to approve. Must match the submitted result stored during `submitDkgResult`. |

### notifySeedTimeout

```solidity
function notifySeedTimeout() external
```

Notifies about seed for DKG delivery timeout. It is expected
that a seed is delivered by the Random Beacon as a relay entry in a
callback function.

### notifyDkgTimeout

```solidity
function notifyDkgTimeout() external
```

Notifies about DKG timeout.

### challengeDkgResult

```solidity
function challengeDkgResult(struct EcdsaDkg.Result dkgResult) external
```

Challenges DKG result. If the submitted result is proved to be
invalid it reverts the DKG back to the result submission phase.

Due to EIP-150 1/64 of the gas is not forwarded to the call, and
will be kept to execute the remaining operations in the function
after the call inside the try-catch. To eliminate a class of
attacks related to the gas limit manipulation, this function
requires an extra amount of gas to be left at the end of the
execution.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| dkgResult | struct EcdsaDkg.Result | Result to challenge. Must match the submitted result stored during `submitDkgResult`. |

### notifyOperatorInactivity

```solidity
function notifyOperatorInactivity(struct EcdsaInactivity.Claim claim, uint256 nonce, uint32[] groupMembers) external
```

Notifies about operators who are inactive. Using this function,
a majority of the wallet signing group can decide about
punishing specific group members who constantly fail doing their
job. If the provided claim is proved to be valid and signed by
sufficient number of group members, operators of members deemed
as inactive are banned from sortition pool rewards for the
duration specified by `sortitionPoolRewardsBanDuration` parameter.
The function allows to signal about single operators being
inactive as well as to signal wallet-wide heartbeat failures
that are propagated to the wallet owner who should begin the
procedure of moving responsibilities to another wallet given
that the wallet who failed the heartbeat may soon be not able to
function and provide new signatures.
The sender of the claim must be one of the claim signers. This
function can be called only for registered wallets

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| claim | struct EcdsaInactivity.Claim | Operator inactivity claim. |
| nonce | uint256 | Current inactivity claim nonce for the given wallet signing group. Must be the same as the stored one. |
| groupMembers | uint32[] | Identifiers of the wallet signing group members. |

### seize

```solidity
function seize(uint96 amount, uint256 rewardMultiplier, address notifier, bytes32 walletID, uint32[] walletMembersIDs) external
```

Allows the wallet owner to add all signing group members of the
wallet with the given ID to the slashing queue of the staking .
contract. The notifier will receive reward per each group member
from the staking contract notifiers treasury. The reward is
scaled by the `rewardMultiplier` provided as a parameter.

Requirements:
- The expression `keccak256(abi.encode(walletMembersIDs))` must
be exactly the same as the hash stored under `membersIdsHash`
for the given `walletID`. Those IDs are not directly stored
in the contract for gas efficiency purposes but they can be
read from appropriate `DkgResultSubmitted` and `DkgResultApproved`
events.
- `rewardMultiplier` must be between [0, 100].
- This function does revert if staking contract call reverts.
The calling code needs to handle the potential revert.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint96 | Amount of tokens to seize from each signing group member. |
| rewardMultiplier | uint256 | Fraction of the staking contract notifiers reward the notifier should receive; should be between [0, 100]. |
| notifier | address | Address of the misbehavior notifier. |
| walletID | bytes32 | ID of the wallet. |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members. |

### isDkgResultValid

```solidity
function isDkgResultValid(struct EcdsaDkg.Result result) external view returns (bool, string)
```

Checks if DKG result is valid for the current DKG.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| result | struct EcdsaDkg.Result | DKG result. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if the result is valid. If the result is invalid it returns false and an error message. |
| [1] | string |  |

### getWalletCreationState

```solidity
function getWalletCreationState() external view returns (enum EcdsaDkg.State)
```

Check current wallet creation state.

### isWalletMember

```solidity
function isWalletMember(bytes32 walletID, uint32[] walletMembersIDs, address operator, uint256 walletMemberIndex) external view returns (bool)
```

Checks whether the given operator is a member of the given
wallet signing group.

Requirements:
- The `operator` parameter must be an actual sortition pool operator.
- The expression `keccak256(abi.encode(walletMembersIDs))` must
be exactly the same as the hash stored under `membersIdsHash`
for the given `walletID`. Those IDs are not directly stored
in the contract for gas efficiency purposes but they can be
read from appropriate `DkgResultSubmitted` and `DkgResultApproved`
events.
- The `walletMemberIndex` must be in range [1, walletMembersIDs.length]

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletID | bytes32 | ID of the wallet. |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members. |
| operator | address | Address of the checked operator. |
| walletMemberIndex | uint256 | Position of the operator in the wallet signing group members list. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True - if the operator is a member of the given wallet signing group. False - otherwise. |

### hasSeedTimedOut

```solidity
function hasSeedTimedOut() external view returns (bool)
```

Checks if awaiting seed timed out.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if awaiting seed timed out, false otherwise. |

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

### getWallet

```solidity
function getWallet(bytes32 walletID) external view returns (struct Wallets.Wallet)
```

### getWalletPublicKey

```solidity
function getWalletPublicKey(bytes32 walletID) external view returns (bytes)
```

Gets public key of a wallet with a given wallet ID.
The public key is returned in an uncompressed format as a 64-byte
concatenation of X and Y coordinates.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletID | bytes32 | ID of the wallet. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bytes | Uncompressed public key of the wallet. |

### isWalletRegistered

```solidity
function isWalletRegistered(bytes32 walletID) external view returns (bool)
```

Checks if a wallet with the given ID is registered.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletID | bytes32 | Wallet's ID. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if wallet is registered, false otherwise. |

### minimumAuthorization

```solidity
function minimumAuthorization() external view returns (uint96)
```

The minimum authorization amount required so that operator can
participate in ECDSA Wallet operations.

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

The minimum authorization is also returned by `minimumAuthorization()`
function, as a requirement of `IApplication` interface.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| minimumAuthorization | uint96 | The minimum authorization amount required so that operator can participate in the random beacon. This amount is required to execute slashing for providing a malicious DKG result or when a relay entry times out. |
| authorizationDecreaseDelay | uint64 | Delay in seconds that needs to pass between the time authorization decrease is requested and the time that request gets approved. Protects against free-riders earning rewards and not being active in the network. |
| authorizationDecreaseChangePeriod | uint64 | Authorization decrease change period in seconds. It is the time, before authorization decrease delay end, during which the pending authorization decrease request can be overwritten. If set to 0, pending authorization decrease request can not be overwritten until the entire `authorizationDecreaseDelay` ends. If set to value equal `authorizationDecreaseDelay`, request can always be overwritten. |

### rewardParameters

```solidity
function rewardParameters() external view returns (uint256 maliciousDkgResultNotificationRewardMultiplier, uint256 sortitionPoolRewardsBanDuration)
```

Retrieves reward-related parameters.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| maliciousDkgResultNotificationRewardMultiplier | uint256 | Percentage of the staking contract malicious behavior notification reward which will be transferred to the notifier reporting about a malicious DKG result. Notifiers are rewarded from a notifiers treasury pool. For example, if notification reward is 1000 and the value of the multiplier is 5, the notifier will receive: 5% of 1000 = 50 per each operator affected. |
| sortitionPoolRewardsBanDuration | uint256 | Duration of the sortition pool rewards ban imposed on operators who missed their turn for DKG result submission or who failed a heartbeat. |

### slashingParameters

```solidity
function slashingParameters() external view returns (uint96 maliciousDkgResultSlashingAmount)
```

Retrieves slashing-related parameters.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| maliciousDkgResultSlashingAmount | uint96 | Slashing amount for submitting a malicious DKG result. Every DKG result submitted can be challenged for the time of `dkg.resultChallengePeriodLength`. If the DKG result submitted is challenged and proven to be malicious, the operator who submitted the malicious result is slashed for `_maliciousDkgResultSlashingAmount`. |

### gasParameters

```solidity
function gasParameters() external view returns (uint256 dkgResultSubmissionGas, uint256 dkgResultApprovalGasOffset, uint256 notifyOperatorInactivityGasOffset, uint256 notifySeedTimeoutGasOffset, uint256 notifyDkgTimeoutNegativeGasOffset)
```

Retrieves gas-related parameters.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| dkgResultSubmissionGas | uint256 | Calculated max gas cost for submitting a DKG result. This will be refunded as part of the DKG approval process. It is in the submitter's interest to not skip his priority turn on the approval, otherwise the refund of the DKG submission will be refunded to another group member that will call the DKG approve function. |
| dkgResultApprovalGasOffset | uint256 | Gas that is meant to balance the DKG result approval's overall cost. It can be updated by the governance based on the current market conditions. |
| notifyOperatorInactivityGasOffset | uint256 | Gas that is meant to balance the notification of an operator inactivity. It can be updated by the governance based on the current market conditions. |
| notifySeedTimeoutGasOffset | uint256 | Gas that is meant to balance the notification of a seed for DKG delivery timeout. It can be updated by the governance based on the current market conditions. |
| notifyDkgTimeoutNegativeGasOffset | uint256 | Gas that is meant to balance the notification of a DKG protocol execution timeout. It can be updated by the governance based on the current market conditions. |

## WalletRegistryGovernance

Owns the `WalletRegistry` contract and is responsible for updating
its governable parameters in respect to the governance delay.

### newGovernanceDelay

```solidity
uint256 newGovernanceDelay
```

### governanceDelayChangeInitiated

```solidity
uint256 governanceDelayChangeInitiated
```

### newWalletRegistryGovernance

```solidity
address newWalletRegistryGovernance
```

### walletRegistryGovernanceTransferInitiated

```solidity
uint256 walletRegistryGovernanceTransferInitiated
```

### newWalletOwner

```solidity
address newWalletOwner
```

### walletOwnerChangeInitiated

```solidity
uint256 walletOwnerChangeInitiated
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

### newMaliciousDkgResultSlashingAmount

```solidity
uint96 newMaliciousDkgResultSlashingAmount
```

### maliciousDkgResultSlashingAmountChangeInitiated

```solidity
uint256 maliciousDkgResultSlashingAmountChangeInitiated
```

### newMaliciousDkgResultNotificationRewardMultiplier

```solidity
uint256 newMaliciousDkgResultNotificationRewardMultiplier
```

### maliciousDkgResultNotificationRewardMultiplierChangeInitiated

```solidity
uint256 maliciousDkgResultNotificationRewardMultiplierChangeInitiated
```

### newSortitionPoolRewardsBanDuration

```solidity
uint256 newSortitionPoolRewardsBanDuration
```

### sortitionPoolRewardsBanDurationChangeInitiated

```solidity
uint256 sortitionPoolRewardsBanDurationChangeInitiated
```

### newDkgSeedTimeout

```solidity
uint256 newDkgSeedTimeout
```

### dkgSeedTimeoutChangeInitiated

```solidity
uint256 dkgSeedTimeoutChangeInitiated
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

### newSubmitterPrecedencePeriodLength

```solidity
uint256 newSubmitterPrecedencePeriodLength
```

### dkgSubmitterPrecedencePeriodLengthChangeInitiated

```solidity
uint256 dkgSubmitterPrecedencePeriodLengthChangeInitiated
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

### newNotifySeedTimeoutGasOffset

```solidity
uint256 newNotifySeedTimeoutGasOffset
```

### notifySeedTimeoutGasOffsetChangeInitiated

```solidity
uint256 notifySeedTimeoutGasOffsetChangeInitiated
```

### newNotifyDkgTimeoutNegativeGasOffset

```solidity
uint256 newNotifyDkgTimeoutNegativeGasOffset
```

### notifyDkgTimeoutNegativeGasOffsetChangeInitiated

```solidity
uint256 notifyDkgTimeoutNegativeGasOffsetChangeInitiated
```

### newReimbursementPool

```solidity
address payable newReimbursementPool
```

### reimbursementPoolChangeInitiated

```solidity
uint256 reimbursementPoolChangeInitiated
```

### walletRegistry

```solidity
contract WalletRegistry walletRegistry
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

### WalletRegistryGovernanceTransferStarted

```solidity
event WalletRegistryGovernanceTransferStarted(address newWalletRegistryGovernance, uint256 timestamp)
```

### WalletRegistryGovernanceTransferred

```solidity
event WalletRegistryGovernanceTransferred(address newWalletRegistryGovernance)
```

### WalletOwnerUpdateStarted

```solidity
event WalletOwnerUpdateStarted(address walletOwner, uint256 timestamp)
```

### WalletOwnerUpdated

```solidity
event WalletOwnerUpdated(address walletOwner)
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

### MaliciousDkgResultSlashingAmountUpdateStarted

```solidity
event MaliciousDkgResultSlashingAmountUpdateStarted(uint256 maliciousDkgResultSlashingAmount, uint256 timestamp)
```

### MaliciousDkgResultSlashingAmountUpdated

```solidity
event MaliciousDkgResultSlashingAmountUpdated(uint256 maliciousDkgResultSlashingAmount)
```

### MaliciousDkgResultNotificationRewardMultiplierUpdateStarted

```solidity
event MaliciousDkgResultNotificationRewardMultiplierUpdateStarted(uint256 maliciousDkgResultNotificationRewardMultiplier, uint256 timestamp)
```

### MaliciousDkgResultNotificationRewardMultiplierUpdated

```solidity
event MaliciousDkgResultNotificationRewardMultiplierUpdated(uint256 maliciousDkgResultNotificationRewardMultiplier)
```

### SortitionPoolRewardsBanDurationUpdateStarted

```solidity
event SortitionPoolRewardsBanDurationUpdateStarted(uint256 sortitionPoolRewardsBanDuration, uint256 timestamp)
```

### SortitionPoolRewardsBanDurationUpdated

```solidity
event SortitionPoolRewardsBanDurationUpdated(uint256 sortitionPoolRewardsBanDuration)
```

### DkgSeedTimeoutUpdateStarted

```solidity
event DkgSeedTimeoutUpdateStarted(uint256 dkgSeedTimeout, uint256 timestamp)
```

### DkgSeedTimeoutUpdated

```solidity
event DkgSeedTimeoutUpdated(uint256 dkgSeedTimeout)
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

### NotifySeedTimeoutGasOffsetUpdateStarted

```solidity
event NotifySeedTimeoutGasOffsetUpdateStarted(uint256 notifySeedTimeoutGasOffset, uint256 timestamp)
```

### NotifySeedTimeoutGasOffsetUpdated

```solidity
event NotifySeedTimeoutGasOffsetUpdated(uint256 notifySeedTimeoutGasOffset)
```

### NotifyDkgTimeoutNegativeGasOffsetUpdateStarted

```solidity
event NotifyDkgTimeoutNegativeGasOffsetUpdateStarted(uint256 notifyDkgTimeoutNegativeGasOffset, uint256 timestamp)
```

### NotifyDkgTimeoutNegativeGasOffsetUpdated

```solidity
event NotifyDkgTimeoutNegativeGasOffsetUpdated(uint256 notifyDkgTimeoutNegativeGasOffset)
```

### ReimbursementPoolUpdateStarted

```solidity
event ReimbursementPoolUpdateStarted(address reimbursementPool, uint256 timestamp)
```

### ReimbursementPoolUpdated

```solidity
event ReimbursementPoolUpdated(address reimbursementPool)
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
constructor(contract WalletRegistry _walletRegistry, uint256 _governanceDelay) public
```

### upgradeRandomBeacon

```solidity
function upgradeRandomBeacon(address _newRandomBeacon) external
```

Upgrades the random beacon.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRandomBeacon | address | New random beacon address |

### initializeWalletOwner

```solidity
function initializeWalletOwner(address _walletOwner) external
```

Initializes the Wallet Owner's address.

Can be called only by the contract owner. It can be called only if
walletOwner has not been set before. It doesn't enforce a governance
delay for the initial update. Any subsequent updates should be performed
with beginWalletOwnerUpdate/finalizeWalletOwnerUpdate with respect
of a governance delay.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _walletOwner | address | The Wallet Owner's address |

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

### beginWalletRegistryGovernanceTransfer

```solidity
function beginWalletRegistryGovernanceTransfer(address _newWalletRegistryGovernance) external
```

Begins the wallet registry governance transfer process.

Can be called only by the contract owner.

### finalizeWalletRegistryGovernanceTransfer

```solidity
function finalizeWalletRegistryGovernanceTransfer() external
```

Finalizes the wallet registry governance transfer process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginWalletOwnerUpdate

```solidity
function beginWalletOwnerUpdate(address _newWalletOwner) external
```

Begins the wallet owner update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newWalletOwner | address | New wallet owner address |

### finalizeWalletOwnerUpdate

```solidity
function finalizeWalletOwnerUpdate() external
```

Finalizes the wallet owner update process.

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

### beginMaliciousDkgResultNotificationRewardMultiplierUpdate

```solidity
function beginMaliciousDkgResultNotificationRewardMultiplierUpdate(uint256 _newMaliciousDkgResultNotificationRewardMultiplier) external
```

Begins the DKG malicious result notification reward multiplier
update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMaliciousDkgResultNotificationRewardMultiplier | uint256 | New DKG malicious result notification reward multiplier. |

### finalizeMaliciousDkgResultNotificationRewardMultiplierUpdate

```solidity
function finalizeMaliciousDkgResultNotificationRewardMultiplierUpdate() external
```

Finalizes the DKG malicious result notification reward
multiplier update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginDkgResultSubmissionGasUpdate

```solidity
function beginDkgResultSubmissionGasUpdate(uint256 _newDkgResultSubmissionGas) external
```

Begins the dkg result submission gas update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDkgResultSubmissionGas | uint256 | New DKG result submission gas. |

### finalizeDkgResultSubmissionGasUpdate

```solidity
function finalizeDkgResultSubmissionGasUpdate() external
```

Finalizes the dkg result submission gas update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginDkgResultApprovalGasOffsetUpdate

```solidity
function beginDkgResultApprovalGasOffsetUpdate(uint256 _newDkgResultApprovalGasOffset) external
```

Begins the dkg approval gas offset update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDkgResultApprovalGasOffset | uint256 | New DKG result approval gas. |

### finalizeDkgResultApprovalGasOffsetUpdate

```solidity
function finalizeDkgResultApprovalGasOffsetUpdate() external
```

Finalizes the dkg result approval gas offset update process.

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

### beginNotifySeedTimeoutGasOffsetUpdate

```solidity
function beginNotifySeedTimeoutGasOffsetUpdate(uint256 _newNotifySeedTimeoutGasOffset) external
```

Begins the notify seed for DKG delivery timeout gas offset update
process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newNotifySeedTimeoutGasOffset | uint256 | New seed for DKG delivery timeout notification gas offset |

### finalizeNotifySeedTimeoutGasOffsetUpdate

```solidity
function finalizeNotifySeedTimeoutGasOffsetUpdate() external
```

Finalizes the notify seed for DKG delivery timeout gas offset
update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginNotifyDkgTimeoutNegativeGasOffsetUpdate

```solidity
function beginNotifyDkgTimeoutNegativeGasOffsetUpdate(uint256 _newNotifyDkgTimeoutNegativeGasOffset) external
```

Begins the notify DKG timeout negative gas offset update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newNotifyDkgTimeoutNegativeGasOffset | uint256 | New DKG timeout negative gas notification gas offset |

### finalizeNotifyDkgTimeoutNegativeGasOffsetUpdate

```solidity
function finalizeNotifyDkgTimeoutNegativeGasOffsetUpdate() external
```

Finalizes the notify DKG timeout negative gas offset update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginReimbursementPoolUpdate

```solidity
function beginReimbursementPoolUpdate(address payable _newReimbursementPool) external
```

Begins the reimbursement pool update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newReimbursementPool | address payable | New reimbursement pool. |

### finalizeReimbursementPoolUpdate

```solidity
function finalizeReimbursementPoolUpdate() external
```

Finalizes the reimbursement pool update process.

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

### beginDkgSeedTimeoutUpdate

```solidity
function beginDkgSeedTimeoutUpdate(uint256 _newDkgSeedTimeout) external
```

Begins the DKG seed timeout update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDkgSeedTimeout | uint256 | New DKG seed timeout in blocks |

### finalizeDkgSeedTimeoutUpdate

```solidity
function finalizeDkgSeedTimeoutUpdate() external
```

Finalizes the DKG seed timeout update process.

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
function beginDkgSubmitterPrecedencePeriodLengthUpdate(uint256 _newSubmitterPrecedencePeriodLength) external
```

Begins the DKG submitter precedence period length update
process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newSubmitterPrecedencePeriodLength | uint256 | New DKG submitter precedence period length in blocks |

### finalizeDkgSubmitterPrecedencePeriodLengthUpdate

```solidity
function finalizeDkgSubmitterPrecedencePeriodLengthUpdate() external
```

Finalizes the DKG submitter precedence period length update
process.

Can be called only by the contract owner, after the governance
delay elapses.

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

### getRemainingWalletRegistryGovernanceTransferDelayTime

```solidity
function getRemainingWalletRegistryGovernanceTransferDelayTime() external view returns (uint256)
```

Get the time remaining until the wallet registry governance can
be transferred.

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

### getRemainingMaliciousDkgResultNotificationRewardMultiplierUpdateTime

```solidity
function getRemainingMaliciousDkgResultNotificationRewardMultiplierUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG malicious result
notification reward multiplier duration can be updated.

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

### getRemainingDkgSeedTimeoutUpdateTime

```solidity
function getRemainingDkgSeedTimeoutUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG seed timeout can be updated.

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

### getRemainingWalletOwnerUpdateTime

```solidity
function getRemainingWalletOwnerUpdateTime() external view returns (uint256)
```

Get the time remaining until the wallet owner can be updated.

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

### getRemainingDkgResultSubmissionGasUpdateTime

```solidity
function getRemainingDkgResultSubmissionGasUpdateTime() external view returns (uint256)
```

Get the time remaining until the dkg result submission gas can
be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingDkgResultApprovalGasOffsetUpdateTime

```solidity
function getRemainingDkgResultApprovalGasOffsetUpdateTime() external view returns (uint256)
```

Get the time remaining until the dkg result approval gas offset
can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingNotifyOperatorInactivityGasOffsetUpdateTime

```solidity
function getRemainingNotifyOperatorInactivityGasOffsetUpdateTime() external view returns (uint256)
```

Get the time remaining until the operator inactivity gas offset
can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingNotifySeedTimeoutGasOffsetUpdateTime

```solidity
function getRemainingNotifySeedTimeoutGasOffsetUpdateTime() external view returns (uint256)
```

Get the time remaining until the seed for DKG delivery timeout
gas offset can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingNotifyDkgTimeoutNegativeGasOffsetUpdateTime

```solidity
function getRemainingNotifyDkgTimeoutNegativeGasOffsetUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG timeout negative gas offset
can be updated.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

### getRemainingReimbursementPoolUpdateTime

```solidity
function getRemainingReimbursementPoolUpdateTime() external view returns (uint256)
```

Get the time remaining until reimbursement pool can be updated.

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

## IWalletOwner

### __ecdsaWalletCreatedCallback

```solidity
function __ecdsaWalletCreatedCallback(bytes32 walletID, bytes32 publicKeyX, bytes32 publicKeyY) external
```

Callback function executed once a new wallet is created.

Should be callable only by the Wallet Registry.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletID | bytes32 | Wallet's unique identifier. |
| publicKeyX | bytes32 |  |
| publicKeyY | bytes32 | Wallet's public key's X coordinate. |

### __ecdsaWalletHeartbeatFailedCallback

```solidity
function __ecdsaWalletHeartbeatFailedCallback(bytes32 walletID, bytes32 publicKeyX, bytes32 publicKeyY) external
```

Callback function executed once a wallet heartbeat failure
is detected.

Should be callable only by the Wallet Registry.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletID | bytes32 | Wallet's unique identifier. |
| publicKeyX | bytes32 |  |
| publicKeyY | bytes32 | Wallet's public key's X coordinate. |

## IWalletRegistry

### requestNewWallet

```solidity
function requestNewWallet() external
```

Requests a new wallet creation.

Only the Wallet Owner can call this function.

### closeWallet

```solidity
function closeWallet(bytes32 walletID) external
```

Closes an existing wallet.

Only the Wallet Owner can call this function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletID | bytes32 | ID of the wallet. |

### seize

```solidity
function seize(uint96 amount, uint256 rewardMultiplier, address notifier, bytes32 walletID, uint32[] walletMembersIDs) external
```

Adds all signing group members of the wallet with the given ID
to the slashing queue of the staking contract. The notifier will
receive reward per each group member from the staking contract
notifiers treasury. The reward is scaled by the
`rewardMultiplier` provided as a parameter.

Only the Wallet Owner can call this function.
Requirements:
- The expression `keccak256(abi.encode(walletMembersIDs))` must
be exactly the same as the hash stored under `membersIdsHash`
for the given `walletID`. Those IDs are not directly stored
in the contract for gas efficiency purposes but they can be
read from appropriate `DkgResultSubmitted` and `DkgResultApproved`
events.
- `rewardMultiplier` must be between [0, 100].
- This function does revert if staking contract call reverts.
The calling code needs to handle the potential revert.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint96 | Amount of tokens to seize from each signing group member |
| rewardMultiplier | uint256 | Fraction of the staking contract notifiers reward the notifier should receive; should be between [0, 100] |
| notifier | address | Address of the misbehavior notifier |
| walletID | bytes32 | ID of the wallet |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members |

### getWalletPublicKey

```solidity
function getWalletPublicKey(bytes32 walletID) external view returns (bytes)
```

Gets public key of a wallet with a given wallet ID.
The public key is returned in an uncompressed format as a 64-byte
concatenation of X and Y coordinates.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletID | bytes32 | ID of the wallet. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bytes | Uncompressed public key of the wallet. |

### getWalletCreationState

```solidity
function getWalletCreationState() external view returns (enum EcdsaDkg.State)
```

Check current wallet creation state.

### isWalletMember

```solidity
function isWalletMember(bytes32 walletID, uint32[] walletMembersIDs, address operator, uint256 walletMemberIndex) external view returns (bool)
```

Checks whether the given operator is a member of the given
wallet signing group.

Requirements:
- The `operator` parameter must be an actual sortition pool operator.
- The expression `keccak256(abi.encode(walletMembersIDs))` must
be exactly the same as the hash stored under `membersIdsHash`
for the given `walletID`. Those IDs are not directly stored
in the contract for gas efficiency purposes but they can be
read from appropriate `DkgResultSubmitted` and `DkgResultApproved`
events.
- The `walletMemberIndex` must be in range [1, walletMembersIDs.length]

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletID | bytes32 | ID of the wallet |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members |
| operator | address | Address of the checked operator |
| walletMemberIndex | uint256 | Position of the operator in the wallet signing group members list |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True - if the operator is a member of the given wallet signing group. False - otherwise. |

## EcdsaAuthorization

Library managing the state of stake authorizations for ECDSA
operator contract and the presence of operators in the sortition
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
  struct EcdsaAuthorization.Parameters parameters;
  mapping(address => address) stakingProviderToOperator;
  mapping(address => address) operatorToStakingProvider;
  mapping(address => struct EcdsaAuthorization.AuthorizationDecrease) pendingDecreases;
  uint256[46] __gap;
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

### setMinimumAuthorization

```solidity
function setMinimumAuthorization(struct EcdsaAuthorization.Data self, uint96 _minimumAuthorization) internal
```

Sets the minimum authorization for ECDSA application. Without
at least the minimum authorization, staking provider is not
eligible to join and operate in the network.

### setAuthorizationDecreaseDelay

```solidity
function setAuthorizationDecreaseDelay(struct EcdsaAuthorization.Data self, uint64 _authorizationDecreaseDelay) internal
```

Sets the authorization decrease delay. It is the time in seconds
that needs to pass between the time authorization decrease is
requested and the time the authorization decrease can be
approved, no matter the authorization decrease amount.

### setAuthorizationDecreaseChangePeriod

```solidity
function setAuthorizationDecreaseChangePeriod(struct EcdsaAuthorization.Data self, uint64 _authorizationDecreaseChangePeriod) internal
```

Sets the authorization decrease change period. It is the time
period before the authorization decrease delay end,
during which the authorization decrease request can be
overwritten.

### registerOperator

```solidity
function registerOperator(struct EcdsaAuthorization.Data self, address operator) internal
```

Used by staking provider to set operator address that will
operate ECDSA node. The given staking provider can set operator
address only one time. The operator address can not be changed
and must be unique. Reverts if the operator is already set for
the staking provider or if the operator address is already in
use. Reverts if there is a pending authorization decrease for
the staking provider.

### authorizationIncreased

```solidity
function authorizationIncreased(struct EcdsaAuthorization.Data self, address stakingProvider, uint96 fromAmount, uint96 toAmount) internal
```

Used by T staking contract to inform the application that the
authorized stake amount for the given staking provider increased.

Reverts if the authorization amount is below the minimum.

The function is not updating the sortition pool. Sortition pool
state needs to be updated by the operator with a call to
`joinSortitionPool` or `updateOperatorStatus`.

Should only be callable by T staking contract.

### authorizationDecreaseRequested

```solidity
function authorizationDecreaseRequested(struct EcdsaAuthorization.Data self, address stakingProvider, uint96 fromAmount, uint96 toAmount) internal
```

Used by T staking contract to inform the application that the
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
function approveAuthorizationDecrease(struct EcdsaAuthorization.Data self, contract IStaking tokenStaking, address stakingProvider) internal
```

Approves the previously registered authorization decrease
request. Reverts if authorization decrease delay have not passed
yet or if the authorization decrease was not requested for the
given staking provider.

### involuntaryAuthorizationDecrease

```solidity
function involuntaryAuthorizationDecrease(struct EcdsaAuthorization.Data self, contract IStaking tokenStaking, contract SortitionPool sortitionPool, address stakingProvider, uint96 fromAmount, uint96 toAmount) internal
```

Used by T staking contract to inform the application the
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
function joinSortitionPool(struct EcdsaAuthorization.Data self, contract IStaking tokenStaking, contract SortitionPool sortitionPool) internal
```

Lets the operator join the sortition pool. The operator address
must be known - before calling this function, it has to be
appointed by the staking provider by calling `registerOperator`.
Also, the operator must have the minimum authorization required
by ECDSA. Function reverts if there is no minimum stake
authorized or if the operator is not known. If there was an
authorization decrease requested, it is activated by starting
the authorization decrease delay.

### updateOperatorStatus

```solidity
function updateOperatorStatus(struct EcdsaAuthorization.Data self, contract IStaking tokenStaking, contract SortitionPool sortitionPool, address operator) internal
```

Updates status of the operator in the sortition pool. If there
was an authorization decrease requested, it is activated by
starting the authorization decrease delay.
Function reverts if the operator is not known.

### isOperatorUpToDate

```solidity
function isOperatorUpToDate(struct EcdsaAuthorization.Data self, contract IStaking tokenStaking, contract SortitionPool sortitionPool, address operator) internal view returns (bool)
```

Checks if the operator's authorized stake is in sync with
operator's weight in the sortition pool.
If the operator is not in the sortition pool and their
authorized stake is non-zero, function returns false.

### eligibleStake

```solidity
function eligibleStake(struct EcdsaAuthorization.Data self, contract IStaking tokenStaking, address stakingProvider) internal view returns (uint96)
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
function eligibleStake(struct EcdsaAuthorization.Data self, contract IStaking tokenStaking, address stakingProvider, uint96 decreasingBy) internal view returns (uint96)
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
function pendingAuthorizationDecrease(struct EcdsaAuthorization.Data self, address stakingProvider) internal view returns (uint96)
```

Returns the amount of stake that is pending authorization
decrease for the given staking provider. If no authorization
decrease has been requested, returns zero.

### remainingAuthorizationDecreaseDelay

```solidity
function remainingAuthorizationDecreaseDelay(struct EcdsaAuthorization.Data self, address stakingProvider) internal view returns (uint64)
```

Returns the remaining time in seconds that needs to pass before
the requested authorization decrease can be approved.
If the sortition pool state was not updated yet by the operator
after requesting the authorization decrease, returns
`type(uint64).max`.

## EcdsaDkg

### Parameters

```solidity
struct Parameters {
  uint256 seedTimeout;
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
  contract EcdsaDkgValidator dkgValidator;
  struct EcdsaDkg.Parameters parameters;
  uint256 stateLockBlock;
  uint256 startBlock;
  uint256 seed;
  uint256 resultSubmissionStartBlockOffset;
  bytes32 submittedResultHash;
  uint256 submittedResultBlock;
  uint256[38] __gap;
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
  AWAITING_RESULT,
  CHALLENGE
}
```

### groupSize

```solidity
uint256 groupSize
```

Size of a group in ECDSA wallet.

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

### init

```solidity
function init(struct EcdsaDkg.Data self, contract SortitionPool _sortitionPool, contract EcdsaDkgValidator _dkgValidator) internal
```

Initializes SortitionPool and EcdsaDkgValidator addresses.
Can be performed only once.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct EcdsaDkg.Data |  |
| _sortitionPool | contract SortitionPool | Sortition Pool reference |
| _dkgValidator | contract EcdsaDkgValidator | EcdsaDkgValidator reference |

### currentState

```solidity
function currentState(struct EcdsaDkg.Data self) internal view returns (enum EcdsaDkg.State state)
```

Determines the current state of group creation. It doesn't take
timeouts into consideration. The timeouts should be tracked and
notified separately.

### lockState

```solidity
function lockState(struct EcdsaDkg.Data self) internal
```

Locks the sortition pool and starts awaiting for the
group creation seed.

### start

```solidity
function start(struct EcdsaDkg.Data self, uint256 seed) internal
```

### submitResult

```solidity
function submitResult(struct EcdsaDkg.Data self, struct EcdsaDkg.Result result) internal
```

Allows to submit a DKG result. The submitted result does not go
through a validation and before it gets accepted, it needs to
wait through the challenge period during which everyone has
a chance to challenge the result as invalid one. Submitter of
the result needs to be in the sortition pool and if the result
gets challenged, the submitter will get slashed.

### hasSeedTimedOut

```solidity
function hasSeedTimedOut(struct EcdsaDkg.Data self) internal view returns (bool)
```

Checks if awaiting seed timed out.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if awaiting seed timed out, false otherwise. |

### hasDkgTimedOut

```solidity
function hasDkgTimedOut(struct EcdsaDkg.Data self) internal view returns (bool)
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

### notifySeedTimeout

```solidity
function notifySeedTimeout(struct EcdsaDkg.Data self) internal
```

Notifies about the seed was not delivered and restores the
initial DKG state (IDLE).

### notifyDkgTimeout

```solidity
function notifyDkgTimeout(struct EcdsaDkg.Data self) internal
```

Notifies about DKG timeout.

### approveResult

```solidity
function approveResult(struct EcdsaDkg.Data self, struct EcdsaDkg.Result result) internal returns (uint32[] misbehavedMembers)
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
| self | struct EcdsaDkg.Data |  |
| result | struct EcdsaDkg.Result | Result to approve. Must match the submitted result stored during `submitResult`. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| misbehavedMembers | uint32[] | Identifiers of members who misbehaved during DKG. |

### challengeResult

```solidity
function challengeResult(struct EcdsaDkg.Data self, struct EcdsaDkg.Result result) internal returns (bytes32 maliciousResultHash, uint32 maliciousSubmitter)
```

Challenges DKG result. If the submitted result is proved to be
invalid it reverts the DKG back to the result submission phase.

Can be called during a challenge period for the submitted result.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct EcdsaDkg.Data |  |
| result | struct EcdsaDkg.Result | Result to challenge. Must match the submitted result stored during `submitResult`. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| maliciousResultHash | bytes32 | Hash of the malicious result. |
| maliciousSubmitter | uint32 | Identifier of the malicious submitter. |

### requireChallengeExtraGas

```solidity
function requireChallengeExtraGas(struct EcdsaDkg.Data self) internal view
```

Due to EIP150, 1/64 of the gas is not forwarded to the call, and
will be kept to execute the remaining operations in the function
after the call inside the try-catch.

To ensure there is no way for the caller to manipulate gas limit
in such a way that the call inside try-catch fails with out-of-gas
and the rest of the function is executed with the remaining
1/64 of gas, we require an extra gas amount to be left at the
end of the call to the function challenging DKG result and
wrapping the call to EcdsaDkgValidator and TokenStaking
contracts inside a try-catch.

### isResultValid

```solidity
function isResultValid(struct EcdsaDkg.Data self, struct EcdsaDkg.Result result) internal view returns (bool, string)
```

Checks if DKG result is valid for the current DKG.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct EcdsaDkg.Data |  |
| result | struct EcdsaDkg.Result | DKG result. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if the result is valid. If the result is invalid it returns false and an error message. |
| [1] | string |  |

### setSeedTimeout

```solidity
function setSeedTimeout(struct EcdsaDkg.Data self, uint256 newSeedTimeout) internal
```

Set setSeedTimeout parameter.

### setResultChallengePeriodLength

```solidity
function setResultChallengePeriodLength(struct EcdsaDkg.Data self, uint256 newResultChallengePeriodLength) internal
```

Set resultChallengePeriodLength parameter.

### setResultChallengeExtraGas

```solidity
function setResultChallengeExtraGas(struct EcdsaDkg.Data self, uint256 newResultChallengeExtraGas) internal
```

Set resultChallengeExtraGas parameter.

### setResultSubmissionTimeout

```solidity
function setResultSubmissionTimeout(struct EcdsaDkg.Data self, uint256 newResultSubmissionTimeout) internal
```

Set resultSubmissionTimeout parameter.

### setSubmitterPrecedencePeriodLength

```solidity
function setSubmitterPrecedencePeriodLength(struct EcdsaDkg.Data self, uint256 newSubmitterPrecedencePeriodLength) internal
```

Set submitterPrecedencePeriodLength parameter.

### complete

```solidity
function complete(struct EcdsaDkg.Data self) internal
```

Completes DKG by cleaning up state.

Should be called after DKG times out or a result is approved.

## EcdsaInactivity

### Claim

```solidity
struct Claim {
  bytes32 walletID;
  uint256[] inactiveMembersIndices;
  bool heartbeatFailed;
  bytes signatures;
  uint256[] signingMembersIndices;
}
```

### groupThreshold

```solidity
uint256 groupThreshold
```

The minimum number of wallet signing group members needed to
interact according to the protocol to produce a valid inactivity
claim.

### signatureByteSize

```solidity
uint256 signatureByteSize
```

Size in bytes of a single signature produced by member
supporting the inactivity claim.

### verifyClaim

```solidity
function verifyClaim(contract SortitionPool sortitionPool, struct EcdsaInactivity.Claim claim, bytes walletPubKey, uint256 nonce, uint32[] groupMembers) external view returns (uint32[] inactiveMembers)
```

Verifies the inactivity claim according to the rules defined in
`Claim` struct documentation. Reverts if verification fails.

Wallet signing group members hash is validated upstream in
`WalletRegistry.notifyOperatorInactivity()`

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| sortitionPool | contract SortitionPool | Sortition pool reference |
| claim | struct EcdsaInactivity.Claim | Inactivity claim |
| walletPubKey | bytes | Public key of the wallet |
| nonce | uint256 | Current inactivity nonce for wallet used in the claim |
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
| indices | uint256[] | Array to validate. |
| groupSize | uint256 | Group size used as reference. |

## Wallets

### Wallet

```solidity
struct Wallet {
  bytes32 membersIdsHash;
  bytes32 publicKeyX;
  bytes32 publicKeyY;
}
```

### Data

```solidity
struct Data {
  mapping(bytes32 => struct Wallets.Wallet) registry;
  uint256[49] __gap;
}
```

### validatePublicKey

```solidity
function validatePublicKey(struct Wallets.Data self, bytes publicKey) internal view
```

Performs preliminary validation of a new group public key.
The group public key must be unique and have 64 bytes in length.
If the validation fails, the function reverts. This function
must be called first for a public key of a wallet added with
`addWallet` function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Wallets.Data |  |
| publicKey | bytes | Uncompressed public key of a new wallet. |

### addWallet

```solidity
function addWallet(struct Wallets.Data self, bytes32 membersIdsHash, bytes publicKey) internal returns (bytes32 walletID, bytes32 publicKeyX, bytes32 publicKeyY)
```

Registers a new wallet. This function does not validate
parameters. The code calling this function must call
`validatePublicKey` first.

Uses a public key hash as a unique identifier of a wallet.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Wallets.Data |  |
| membersIdsHash | bytes32 | Keccak256 hash of group members identifiers array |
| publicKey | bytes | Uncompressed public key |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletID | bytes32 | Wallet's ID |
| publicKeyX | bytes32 | Wallet's public key's X coordinate |
| publicKeyY | bytes32 | Wallet's public key's Y coordinate |

### deleteWallet

```solidity
function deleteWallet(struct Wallets.Data self, bytes32 walletID) internal
```

Deletes wallet with the given ID from the registry. Reverts
if wallet with the given ID has not been registered or if it
has already been closed.

### isWalletRegistered

```solidity
function isWalletRegistered(struct Wallets.Data self, bytes32 walletID) internal view returns (bool)
```

Checks if a wallet with the given ID is registered.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Wallets.Data |  |
| walletID | bytes32 | Wallet's ID |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if a wallet is registered, false otherwise |

### getWalletMembersIdsHash

```solidity
function getWalletMembersIdsHash(struct Wallets.Data self, bytes32 walletID) internal view returns (bytes32)
```

Returns Keccak256 hash of the wallet signing group members
identifiers array. Group members do not include operators
selected by the sortition pool that misbehaved during DKG.
Reverts if wallet with the given ID is not registered.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Wallets.Data |  |
| walletID | bytes32 | ID of the wallet |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bytes32 | Wallet signing group members hash |

### getWalletPublicKeyCoordinates

```solidity
function getWalletPublicKeyCoordinates(struct Wallets.Data self, bytes32 walletID) internal view returns (bytes32 x, bytes32 y)
```

Gets public key of a wallet with the given wallet ID.
The public key is returned as X and Y coordinates.
Reverts if wallet with the given ID is not registered.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Wallets.Data |  |
| walletID | bytes32 | ID of the wallet |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| x | bytes32 | Public key X coordinate |
| y | bytes32 | Public key Y coordinate |

### getWalletPublicKey

```solidity
function getWalletPublicKey(struct Wallets.Data self, bytes32 walletID) internal view returns (bytes)
```

Gets public key of a wallet with the given wallet ID.
The public key is returned in an uncompressed format as a 64-byte
concatenation of X and Y coordinates.
Reverts if wallet with the given ID is not registered.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct Wallets.Data |  |
| walletID | bytes32 | ID of the wallet |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bytes | Uncompressed public key of the wallet |

