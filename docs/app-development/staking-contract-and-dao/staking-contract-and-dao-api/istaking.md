# Solidity API

## IStaking

The staking contract enables T owners to have their wallets offline
and their stake managed by staking providers on their behalf.
The staking contract does not define operator role. The operator
responsible for running off-chain client software is appointed by
the staking provider in the particular application utilizing the
staking contract. All off-chain client software should be able
to run without exposing operator's or staking provider’s private
key and should not require any owner’s keys at all. The stake
delegation optimizes the network throughput without compromising the
security of the owners’ stake.

### StakeType

```solidity
enum StakeType {
  NU,
  KEEP,
  T
}
```

### stake

```solidity
function stake(address stakingProvider, address payable beneficiary, address authorizer, uint96 amount) external
```

Creates a delegation with `msg.sender` owner with the given
staking provider, beneficiary, and authorizer. Transfers the
given amount of T to the staking contract.

The owner of the delegation needs to have the amount approved to
transfer to the staking contract.

### stakeKeep

```solidity
function stakeKeep(address stakingProvider) external
```

Copies delegation from the legacy KEEP staking contract to T
staking contract. No tokens are transferred. Caches the active
stake amount from KEEP staking contract. Can be called by
anyone.

The staking provider in T staking contract is the legacy KEEP
staking contract operator.

### stakeNu

```solidity
function stakeNu(address stakingProvider, address payable beneficiary, address authorizer) external
```

Copies delegation from the legacy NU staking contract to T
staking contract, additionally appointing staking provider,
beneficiary and authorizer roles. Caches the amount staked in NU
staking contract. Can be called only by the original delegation
owner.

### setMinimumStakeAmount

```solidity
function setMinimumStakeAmount(uint96 amount) external
```

Allows the Governance to set the minimum required stake amount.
This amount is required to protect against griefing the staking
contract and individual applications are allowed to require
higher minimum stakes if necessary.

### approveApplication

```solidity
function approveApplication(address application) external
```

Allows the Governance to approve the particular application
before individual stake authorizers are able to authorize it.

### increaseAuthorization

```solidity
function increaseAuthorization(address stakingProvider, address application, uint96 amount) external
```

Increases the authorization of the given staking provider for
the given application by the given amount. Can only be called by
the authorizer for that staking provider.

Calls `authorizationIncreased(address stakingProvider, uint256 amount)`
on the given application to notify the application about
authorization change. See `IApplication`.

### requestAuthorizationDecrease

```solidity
function requestAuthorizationDecrease(address stakingProvider, address application, uint96 amount) external
```

Requests decrease of the authorization for the given staking
provider on the given application by the provided amount.
It may not change the authorized amount immediatelly. When
it happens depends on the application. Can only be called by the
given staking provider’s authorizer. Overwrites pending
authorization decrease for the given staking provider and
application if the application agrees for that. If the
application does not agree for overwriting, the function
reverts.

Calls `authorizationDecreaseRequested(address stakingProvider, uint256 amount)`
on the given application. See `IApplication`.

### requestAuthorizationDecrease

```solidity
function requestAuthorizationDecrease(address stakingProvider) external
```

Requests decrease of all authorizations for the given staking
provider on all applications by all authorized amount.
It may not change the authorized amount immediatelly. When
it happens depends on the application. Can only be called by the
given staking provider’s authorizer. Overwrites pending
authorization decrease for the given staking provider and
application.

Calls `authorizationDecreaseRequested(address stakingProvider, uint256 amount)`
for each authorized application. See `IApplication`.

### approveAuthorizationDecrease

```solidity
function approveAuthorizationDecrease(address stakingProvider) external returns (uint96)
```

Called by the application at its discretion to approve the
previously requested authorization decrease request. Can only be
called by the application that was previously requested to
decrease the authorization for that staking provider.
Returns resulting authorized amount for the application.

### forceDecreaseAuthorization

```solidity
function forceDecreaseAuthorization(address stakingProvider, address application) external
```

Decreases the authorization for the given `stakingProvider` on
the given disabled `application`, for all authorized amount.
Can be called by anyone.

### pauseApplication

```solidity
function pauseApplication(address application) external
```

Pauses the given application’s eligibility to slash stakes.
Besides that stakers can't change authorization to the application.
Can be called only by the Panic Button of the particular
application. The paused application can not slash stakes until
it is approved again by the Governance using `approveApplication`
function. Should be used only in case of an emergency.

### disableApplication

```solidity
function disableApplication(address application) external
```

Disables the given application. The disabled application can't
slash stakers. Also stakers can't increase authorization to that
application but can decrease without waiting by calling
`requestAuthorizationDecrease` at any moment. Can be called only
by the governance. The disabled application can't be approved
again. Should be used only in case of an emergency.

### setPanicButton

```solidity
function setPanicButton(address application, address panicButton) external
```

Sets the Panic Button role for the given application to the
provided address. Can only be called by the Governance. If the
Panic Button for the given application should be disabled, the
role address should be set to 0x0 address.

### setAuthorizationCeiling

```solidity
function setAuthorizationCeiling(uint256 ceiling) external
```

Sets the maximum number of applications one staking provider can
have authorized. Used to protect against DoSing slashing queue.
Can only be called by the Governance.

### topUp

```solidity
function topUp(address stakingProvider, uint96 amount) external
```

Increases the amount of the stake for the given staking provider.

The sender of this transaction needs to have the amount approved to
transfer to the staking contract.

### topUpKeep

```solidity
function topUpKeep(address stakingProvider) external
```

Propagates information about stake top-up from the legacy KEEP
staking contract to T staking contract. Can be called only by
the owner or the staking provider.

### topUpNu

```solidity
function topUpNu(address stakingProvider) external
```

Propagates information about stake top-up from the legacy NU
staking contract to T staking contract. Can be called only by
the owner or the staking provider.

### unstakeT

```solidity
function unstakeT(address stakingProvider, uint96 amount) external
```

Reduces the liquid T stake amount by the provided amount and
withdraws T to the owner. Reverts if there is at least one
authorization higher than the sum of the legacy stake and
remaining liquid T stake or if the unstake amount is higher than
the liquid T stake amount. Can be called only by the delegation
owner or the staking provider.

### unstakeKeep

```solidity
function unstakeKeep(address stakingProvider) external
```

Sets the legacy KEEP staking contract active stake amount cached
in T staking contract to 0. Reverts if the amount of liquid T
staked in T staking contract is lower than the highest
application authorization. This function allows to unstake from
KEEP staking contract and still being able to operate in T
network and earning rewards based on the liquid T staked. Can be
called only by the delegation owner or the staking provider.

### unstakeNu

```solidity
function unstakeNu(address stakingProvider, uint96 amount) external
```

Reduces cached legacy NU stake amount by the provided amount.
Reverts if there is at least one authorization higher than the
sum of remaining legacy NU stake and liquid T stake for that
staking provider or if the untaked amount is higher than the
cached legacy stake amount. If succeeded, the legacy NU stake
can be partially or fully undelegated on the legacy staking
contract. This function allows to unstake from NU staking
contract and still being able to operate in T network and
earning rewards based on the liquid T staked. Can be called only
by the delegation owner or the staking provider.

### unstakeAll

```solidity
function unstakeAll(address stakingProvider) external
```

Sets cached legacy stake amount to 0, sets the liquid T stake
amount to 0 and withdraws all liquid T from the stake to the
owner. Reverts if there is at least one non-zero authorization.
Can be called only by the delegation owner or the staking
provider.

### notifyKeepStakeDiscrepancy

```solidity
function notifyKeepStakeDiscrepancy(address stakingProvider) external
```

Notifies about the discrepancy between legacy KEEP active stake
and the amount cached in T staking contract. Slashes the staking
provider in case the amount cached is higher than the actual
active stake amount in KEEP staking contract. Needs to update
authorizations of all affected applications and execute an
involuntary allocation decrease on all affected applications.
Can be called by anyone, notifier receives a reward.

### notifyNuStakeDiscrepancy

```solidity
function notifyNuStakeDiscrepancy(address stakingProvider) external
```

Notifies about the discrepancy between legacy NU active stake
and the amount cached in T staking contract. Slashes the
staking provider in case the amount cached is higher than the
actual active stake amount in NU staking contract. Needs to
update authorizations of all affected applications and execute
an involuntary allocation decrease on all affected applications.
Can be called by anyone, notifier receives a reward.

### setStakeDiscrepancyPenalty

```solidity
function setStakeDiscrepancyPenalty(uint96 penalty, uint256 rewardMultiplier) external
```

Sets the penalty amount for stake discrepancy and reward
multiplier for reporting it. The penalty is seized from the
delegated stake, and 5% of the penalty, scaled by the
multiplier, is given to the notifier. The rest of the tokens are
burned. Can only be called by the Governance. See `seize` function.

### setNotificationReward

```solidity
function setNotificationReward(uint96 reward) external
```

Sets reward in T tokens for notification of misbehaviour
of one staking provider. Can only be called by the governance.

### pushNotificationReward

```solidity
function pushNotificationReward(uint96 reward) external
```

Transfer some amount of T tokens as reward for notifications
of misbehaviour

### withdrawNotificationReward

```solidity
function withdrawNotificationReward(address recipient, uint96 amount) external
```

Withdraw some amount of T tokens from notifiers treasury.
Can only be called by the governance.

### slash

```solidity
function slash(uint96 amount, address[] stakingProviders) external
```

Adds staking providers to the slashing queue along with the
amount that should be slashed from each one of them. Can only be
called by application authorized for all staking providers in
the array.

### seize

```solidity
function seize(uint96 amount, uint256 rewardMultipier, address notifier, address[] stakingProviders) external
```

Adds staking providers to the slashing queue along with the
amount. The notifier will receive reward per each staking
provider from notifiers treasury. Can only be called by
application authorized for all staking providers in the array.

### processSlashing

```solidity
function processSlashing(uint256 count) external
```

Takes the given number of queued slashing operations and
processes them. Receives 5% of the slashed amount.
Executes `involuntaryAllocationDecrease` function on each
affected application.

### authorizedStake

```solidity
function authorizedStake(address stakingProvider, address application) external view returns (uint96)
```

Returns the authorized stake amount of the staking provider for
the application.

### stakes

```solidity
function stakes(address stakingProvider) external view returns (uint96 tStake, uint96 keepInTStake, uint96 nuInTStake)
```

Returns staked amount of T, Keep and Nu for the specified
staking provider.

All values are in T denomination

### getStartStakingTimestamp

```solidity
function getStartStakingTimestamp(address stakingProvider) external view returns (uint256)
```

Returns start staking timestamp.

This value is set at most once.

### stakedNu

```solidity
function stakedNu(address stakingProvider) external view returns (uint256)
```

Returns staked amount of NU for the specified staking provider.

### rolesOf

```solidity
function rolesOf(address stakingProvider) external view returns (address owner, address payable beneficiary, address authorizer)
```

Gets the stake owner, the beneficiary and the authorizer
for the specified staking provider address.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| owner | address | Stake owner address. |
| beneficiary | address payable | Beneficiary address. |
| authorizer | address | Authorizer address. |

### getApplicationsLength

```solidity
function getApplicationsLength() external view returns (uint256)
```

Returns length of application array

### getSlashingQueueLength

```solidity
function getSlashingQueueLength() external view returns (uint256)
```

Returns length of slashing queue

### getMinStaked

```solidity
function getMinStaked(address stakingProvider, enum IStaking.StakeType stakeTypes) external view returns (uint96)
```

Returns minimum possible stake for T, KEEP or NU in T
denomination.

For example, suppose the given staking provider has 10 T, 20 T
worth of KEEP, and 30 T worth of NU all staked, and the maximum
application authorization is 40 T, then `getMinStaked` for
that staking provider returns:
* 0 T if KEEP stake type specified i.e.
min = 40 T max - (10 T + 30 T worth of NU) = 0 T
* 10 T if NU stake type specified i.e.
min = 40 T max - (10 T + 20 T worth of KEEP) = 10 T
* 0 T if T stake type specified i.e.
min = 40 T max - (20 T worth of KEEP + 30 T worth of NU) < 0 T
In other words, the minimum stake amount for the specified
stake type is the minimum amount of stake of the given type
needed to satisfy the maximum application authorization given the
staked amounts of the other stake types for that staking provider.

### getAvailableToAuthorize

```solidity
function getAvailableToAuthorize(address stakingProvider, address application) external view returns (uint96)
```

Returns available amount to authorize for the specified application

