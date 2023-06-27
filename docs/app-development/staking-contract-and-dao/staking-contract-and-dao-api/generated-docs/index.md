# Solidity API
## Table Of Contents

<!-- toc -->

- [BaseTokenholderGovernor](#basetokenholdergovernor)
- [Checkpoints](#checkpoints)
- [GovernorParameters](#governorparameters)
- [IVotesHistory](#ivoteshistory)
- [ProxyAdminWithDeputy](#proxyadminwithdeputy)
- [StakerGovernor](#stakergovernor)
- [StakerGovernorVotes](#stakergovernorvotes)
- [TokenholderGovernor](#tokenholdergovernor)
- [TokenholderGovernorVotes](#tokenholdergovernorvotes)
- [IApplication](#iapplication)
- [IKeepTokenStaking](#ikeeptokenstaking)
- [INuCypherStakingEscrow](#inucypherstakingescrow)
- [IStaking](#istaking)
- [IManagedGrant](#imanagedgrant)
- [KeepStake](#keepstake)
- [TokenStaking](#tokenstaking)
- [T](#t)
- [PercentUtils](#percentutils)
- [SafeTUpgradeable](#safetupgradeable)
- [VendingMachine](#vendingmachine)

<!-- tocstop -->

## BaseTokenholderGovernor

### VETO_POWER

```solidity
bytes32 VETO_POWER
```

### constructor

```solidity
constructor(contract T _token, contract IVotesHistory _staking, contract TimelockController _timelock, address _vetoer, uint256 _quorumNumerator, uint256 _proposalThresholdNumerator, uint256 votingDelay, uint256 votingPeriod, uint64 votingExtension) public
```

### cancel

```solidity
function cancel(address[] targets, uint256[] values, bytes[] calldatas, bytes32 descriptionHash) external returns (uint256)
```

### propose

```solidity
function propose(address[] targets, uint256[] values, bytes[] calldatas, string description) public returns (uint256)
```

### quorum

```solidity
function quorum(uint256 blockNumber) public view returns (uint256)
```

### proposalThreshold

```solidity
function proposalThreshold() public view returns (uint256)
```

### getVotes

```solidity
function getVotes(address account, uint256 blockNumber) public view returns (uint256)
```

### state

```solidity
function state(uint256 proposalId) public view returns (enum IGovernor.ProposalState)
```

### supportsInterface

```solidity
function supportsInterface(bytes4 interfaceId) public view returns (bool)
```

### _execute

```solidity
function _execute(uint256 proposalId, address[] targets, uint256[] values, bytes[] calldatas, bytes32 descriptionHash) internal
```

### _cancel

```solidity
function _cancel(address[] targets, uint256[] values, bytes[] calldatas, bytes32 descriptionHash) internal returns (uint256)
```

### _executor

```solidity
function _executor() internal view returns (address)
```

### proposalDeadline

```solidity
function proposalDeadline(uint256 proposalId) public view virtual returns (uint256)
```

### _castVote

```solidity
function _castVote(uint256 proposalId, address account, uint8 support, string reason) internal virtual returns (uint256)
```

## Checkpoints

Abstract contract to support checkpoints for Compound-like voting and
delegation. This implementation supports token supply up to 2^96 - 1.
This contract keeps a history (checkpoints) of each account's vote
power. Vote power can be delegated either by calling the {delegate}
function directly, or by providing a signature to be used with
{delegateBySig}. Voting power can be publicly queried through
{getVotes} and {getPastVotes}.
NOTE: Extracted from OpenZeppelin ERCVotes.sol.
This contract is upgrade-safe.

### Checkpoint

```solidity
struct Checkpoint {
  uint32 fromBlock;
  uint96 votes;
}
```

### _delegates

```solidity
mapping(address => address) _delegates
```

### _checkpoints

```solidity
mapping(address => uint128[]) _checkpoints
```

### _totalSupplyCheckpoints

```solidity
uint128[] _totalSupplyCheckpoints
```

### DelegateChanged

```solidity
event DelegateChanged(address delegator, address fromDelegate, address toDelegate)
```

Emitted when an account changes their delegate.

### DelegateVotesChanged

```solidity
event DelegateVotesChanged(address delegate, uint256 previousBalance, uint256 newBalance)
```

Emitted when a balance or delegate change results in changes
to an account's voting power.

### checkpoints

```solidity
function checkpoints(address account, uint32 pos) public view virtual returns (struct Checkpoints.Checkpoint checkpoint)
```

### numCheckpoints

```solidity
function numCheckpoints(address account) public view virtual returns (uint32)
```

Get number of checkpoints for `account`.

### delegates

```solidity
function delegates(address account) public view virtual returns (address)
```

Get the address `account` is currently delegating to.

### getVotes

```solidity
function getVotes(address account) public view returns (uint96)
```

Gets the current votes balance for `account`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| account | address | The address to get votes balance |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint96 | The number of current votes for `account` |

### getPastVotes

```solidity
function getPastVotes(address account, uint256 blockNumber) public view returns (uint96)
```

Determine the prior number of votes for an account as of
a block number.

Block number must be a finalized block or else this function will
revert to prevent misinformation.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| account | address | The address of the account to check |
| blockNumber | uint256 | The block number to get the vote balance at |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint96 | The number of votes the account had as of the given block |

### getPastTotalSupply

```solidity
function getPastTotalSupply(uint256 blockNumber) public view returns (uint96)
```

Retrieve the `totalSupply` at the end of `blockNumber`.
Note, this value is the sum of all balances, but it is NOT the
sum of all the delegated votes!

`blockNumber` must have been already mined

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| blockNumber | uint256 | The block number to get the total supply at |

### delegate

```solidity
function delegate(address delegator, address delegatee) internal virtual
```

Change delegation for `delegator` to `delegatee`.

### moveVotingPower

```solidity
function moveVotingPower(address src, address dst, uint256 amount) internal
```

Moves voting power from one delegate to another

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| src | address | Address of old delegate |
| dst | address | Address of new delegate |
| amount | uint256 | Voting power amount to transfer between delegates |

### writeCheckpoint

```solidity
function writeCheckpoint(uint128[] ckpts, function (uint256,uint256) view returns (uint256) op, uint256 delta) internal returns (uint256 oldWeight, uint256 newWeight)
```

Writes a new checkpoint based on operating last stored value
with a `delta`. Usually, said operation is the `add` or
`subtract` functions from this contract, but more complex
functions can be passed as parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| ckpts | uint128[] | The checkpoints array to use |
| op | function (uint256,uint256) view returns (uint256) | The function to apply over the last value and the `delta` |
| delta | uint256 | Variation with respect to last stored value to be used for new checkpoint |

### lookupCheckpoint

```solidity
function lookupCheckpoint(uint128[] ckpts, uint256 blockNumber) internal view returns (uint96)
```

Lookup a value in a list of (sorted) checkpoints.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| ckpts | uint128[] | The checkpoints array to use |
| blockNumber | uint256 | Block number when we want to get the checkpoint at |

### maxSupply

```solidity
function maxSupply() internal view virtual returns (uint96)
```

Maximum token supply. Defaults to `type(uint96).max` (2^96 - 1)

### encodeCheckpoint

```solidity
function encodeCheckpoint(uint32 blockNumber, uint96 value) internal pure returns (uint128)
```

Encodes a `blockNumber` and `value` into a single `uint128`
checkpoint.

`blockNumber` is stored in the first 32 bits, while `value` in the
remaining 96 bits.

### decodeBlockNumber

```solidity
function decodeBlockNumber(uint128 checkpoint) internal pure returns (uint32)
```

Decodes a block number from a `uint128` `checkpoint`.

### decodeValue

```solidity
function decodeValue(uint128 checkpoint) internal pure returns (uint96)
```

Decodes a voting value from a `uint128` `checkpoint`.

### decodeCheckpoint

```solidity
function decodeCheckpoint(uint128 checkpoint) internal pure returns (uint32 blockNumber, uint96 value)
```

Decodes a block number and voting value from a `uint128`
`checkpoint`.

### add

```solidity
function add(uint256 a, uint256 b) internal pure returns (uint256)
```

### subtract

```solidity
function subtract(uint256 a, uint256 b) internal pure returns (uint256)
```

## GovernorParameters

Abstract contract to handle governance parameters

Based on `GovernorVotesQuorumFraction`, but without being opinionated
on what's the source of voting power, and extended to handle proposal
thresholds too. See OpenZeppelin's GovernorVotesQuorumFraction,
GovernorVotes and GovernorSettings for reference.

### FRACTION_DENOMINATOR

```solidity
uint256 FRACTION_DENOMINATOR
```

### AVERAGE_BLOCK_TIME_IN_SECONDS

```solidity
uint64 AVERAGE_BLOCK_TIME_IN_SECONDS
```

### quorumNumerator

```solidity
uint256 quorumNumerator
```

### proposalThresholdNumerator

```solidity
uint256 proposalThresholdNumerator
```

### QuorumNumeratorUpdated

```solidity
event QuorumNumeratorUpdated(uint256 oldQuorumNumerator, uint256 newQuorumNumerator)
```

### ProposalThresholdNumeratorUpdated

```solidity
event ProposalThresholdNumeratorUpdated(uint256 oldThresholdNumerator, uint256 newThresholdNumerator)
```

### VotingDelaySet

```solidity
event VotingDelaySet(uint256 oldVotingDelay, uint256 newVotingDelay)
```

### VotingPeriodSet

```solidity
event VotingPeriodSet(uint256 oldVotingPeriod, uint256 newVotingPeriod)
```

### constructor

```solidity
constructor(uint256 quorumNumeratorValue, uint256 proposalNumeratorValue, uint256 initialVotingDelay, uint256 initialVotingPeriod) internal
```

### updateQuorumNumerator

```solidity
function updateQuorumNumerator(uint256 newQuorumNumerator) external virtual
```

### updateProposalThresholdNumerator

```solidity
function updateProposalThresholdNumerator(uint256 newNumerator) external virtual
```

### setVotingDelay

```solidity
function setVotingDelay(uint256 newVotingDelay) external virtual
```

Update the voting delay. This operation can only be performed
through a governance proposal. Emits a `VotingDelaySet` event.

### setVotingPeriod

```solidity
function setVotingPeriod(uint256 newVotingPeriod) external virtual
```

Update the voting period. This operation can only be performed
through a governance proposal. Emits a `VotingPeriodSet` event.

### quorum

```solidity
function quorum(uint256 blockNumber) public view virtual returns (uint256)
```

Compute the required amount of voting power to reach quorum

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| blockNumber | uint256 | The block number to get the quorum at |

### proposalThreshold

```solidity
function proposalThreshold() public view virtual returns (uint256)
```

Compute the required amount of voting power to create a proposal
at the last block height

This function is implemented to comply with Governor API but we
we will actually use `proposalThreshold(uint256 blockNumber)`,
as in our DAOs the threshold amount changes according to supply.

### proposalThreshold

```solidity
function proposalThreshold(uint256 blockNumber) public view returns (uint256)
```

Compute the required amount of voting power to create a proposal

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| blockNumber | uint256 | The block number to get the proposal threshold at |

### votingDelay

```solidity
function votingDelay() public view virtual returns (uint256)
```

module:user-config

Delay, in number of block, between the proposal is created and the vote starts. This can be increassed to
leave time for users to buy voting power, of delegate it, before the voting of a proposal starts.

### votingPeriod

```solidity
function votingPeriod() public view virtual returns (uint256)
```

module:user-config

Delay, in number of blocks, between the vote start and vote ends.

NOTE: The {votingDelay} can delay the start of the vote. This must be considered when setting the voting
duration compared to the voting delay.

### _updateQuorumNumerator

```solidity
function _updateQuorumNumerator(uint256 newQuorumNumerator) internal virtual
```

### _updateProposalThresholdNumerator

```solidity
function _updateProposalThresholdNumerator(uint256 proposalNumerator) internal virtual
```

### _setVotingDelay

```solidity
function _setVotingDelay(uint256 newVotingDelay) internal virtual
```

### _setVotingPeriod

```solidity
function _setVotingPeriod(uint256 newVotingPeriod) internal virtual
```

### _getPastTotalSupply

```solidity
function _getPastTotalSupply(uint256 blockNumber) internal view virtual returns (uint256)
```

Compute the past total voting power at a particular block

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| blockNumber | uint256 | The block number to get the vote power at |

## IVotesHistory

### getPastVotes

```solidity
function getPastVotes(address account, uint256 blockNumber) external view returns (uint96)
```

### getPastTotalSupply

```solidity
function getPastTotalSupply(uint256 blockNumber) external view returns (uint96)
```

## ProxyAdminWithDeputy

Based on `ProxyAdmin`, an auxiliary contract in OpenZeppelin's
upgradeability approach meant to act as the admin of a
`TransparentUpgradeableProxy`. This variant allows an additional
actor, the "deputy", to perform upgrades, which originally can only
be performed by the ProxyAdmin's owner. See OpenZeppelin's
documentation for `TransparentUpgradeableProxy` for more details on
why a ProxyAdmin is recommended.

### deputy

```solidity
address deputy
```

### DeputyUpdated

```solidity
event DeputyUpdated(address previousDeputy, address newDeputy)
```

### onlyOwnerOrDeputy

```solidity
modifier onlyOwnerOrDeputy()
```

### constructor

```solidity
constructor(contract StakerGovernor dao, address _deputy) public
```

### setDeputy

```solidity
function setDeputy(address newDeputy) external
```

### upgrade

```solidity
function upgrade(contract TransparentUpgradeableProxy proxy, address implementation) public virtual
```

Upgrades `proxy` to `implementation`. This contract must be the
admin of `proxy`, and the caller must be this contract's owner
or the deputy.

### upgradeAndCall

```solidity
function upgradeAndCall(contract TransparentUpgradeableProxy proxy, address implementation, bytes data) public payable virtual
```

Upgrades `proxy` to `implementation` and calls a function on the
new implementation. This contract must be the admin of `proxy`,
and the caller must be this contract's owner or the deputy.

### _setDeputy

```solidity
function _setDeputy(address newDeputy) internal
```

### _checkCallerIsOwnerOrDeputy

```solidity
function _checkCallerIsOwnerOrDeputy() internal view
```

## StakerGovernor

### VETO_POWER

```solidity
bytes32 VETO_POWER
```

### manager

```solidity
address manager
```

### constructor

```solidity
constructor(contract IVotesHistory _staking, contract TimelockController _timelock, contract TokenholderGovernor tokenholderGovernor, address vetoer) public
```

### cancel

```solidity
function cancel(address[] targets, uint256[] values, bytes[] calldatas, bytes32 descriptionHash) external returns (uint256)
```

### propose

```solidity
function propose(address[] targets, uint256[] values, bytes[] calldatas, string description) public returns (uint256)
```

### quorum

```solidity
function quorum(uint256 blockNumber) public view returns (uint256)
```

### proposalThreshold

```solidity
function proposalThreshold() public view returns (uint256)
```

### getVotes

```solidity
function getVotes(address account, uint256 blockNumber) public view returns (uint256)
```

### state

```solidity
function state(uint256 proposalId) public view returns (enum IGovernor.ProposalState)
```

### supportsInterface

```solidity
function supportsInterface(bytes4 interfaceId) public view returns (bool)
```

### _execute

```solidity
function _execute(uint256 proposalId, address[] targets, uint256[] values, bytes[] calldatas, bytes32 descriptionHash) internal
```

### _cancel

```solidity
function _cancel(address[] targets, uint256[] values, bytes[] calldatas, bytes32 descriptionHash) internal returns (uint256)
```

### _executor

```solidity
function _executor() internal view returns (address)
```

Returns the address of the entity that acts as governance for
this contract.

By default, Governor assumes this is either the Governor contract
itself, or a timelock if there's one configured. We override this
here for the StakerGovernor contract so it's the Tokenholder DAO's
Timelock, which we obtain at constructor time.

## StakerGovernorVotes

Staker DAO voting power extraction from staked T positions,

### staking

```solidity
contract IVotesHistory staking
```

### constructor

```solidity
constructor(contract IVotesHistory tStakingAddress) internal
```

### getVotes

```solidity
function getVotes(address account, uint256 blockNumber) public view virtual returns (uint256)
```

Read the voting weight from the snapshot mechanism in the T
staking contracts. Note that this also tracks legacy stakes
(NU/KEEP).

See {IGovernor-getVotes}

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| account | address | Delegate account with T staking voting power |
| blockNumber | uint256 | The block number to get the vote balance at |

### _getPastTotalSupply

```solidity
function _getPastTotalSupply(uint256 blockNumber) internal view virtual returns (uint256)
```

Compute the total voting power for the Staker DAO.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| blockNumber | uint256 | The block number to get the voting power at |

## TokenholderGovernor

### constructor

```solidity
constructor(contract T _token, contract IVotesHistory _staking, contract TimelockController _timelock, address vetoer) public
```

## TokenholderGovernorVotes

Tokenholder DAO voting power extraction from both liquid and staked
T token positions, including legacy stakes (NU/KEEP).

### token

```solidity
contract IVotesHistory token
```

### staking

```solidity
contract IVotesHistory staking
```

### constructor

```solidity
constructor(contract IVotesHistory tokenAddress, contract IVotesHistory tStakingAddress) internal
```

### getVotes

```solidity
function getVotes(address account, uint256 blockNumber) public view virtual returns (uint256)
```

Read the voting weight from the snapshot mechanism in the token
and staking contracts. For Tokenholder DAO, there are currently
two voting power sources:
- Liquid T, tracked by the T token contract
- Stakes in the T network, tracked  by the T staking contract.
Note that this also tracks legacy stakes (NU/KEEP); legacy
stakes count for tokenholders' voting power, but not for the
total voting power of the Tokenholder DAO
(see {_getPastTotalSupply}).

See {IGovernor-getVotes}

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| account | address | Tokenholder account in the T network |
| blockNumber | uint256 | The block number to get the vote balance at |

### _getPastTotalSupply

```solidity
function _getPastTotalSupply(uint256 blockNumber) internal view virtual returns (uint256)
```

Compute the total voting power for Tokenholder DAO. Note how it
only uses the token total supply as source, as native T tokens
that are staked continue existing, but as deposits in the
staking contract. However, legacy stakes can't contribute to the
total voting power as they're already implicitly counted as part
of Vending Machines' liquid balance; hence, we only need to read
total voting power from the token.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| blockNumber | uint256 | The block number to get the vote power at |

## IApplication

Generic interface for an application. Application is an external
smart contract or a set of smart contracts utilizing functionalities
offered by Threshold Network. Applications authorized for the given
staking provider are eligible to slash the stake delegated to that
staking provider.

### RewardsWithdrawn

```solidity
event RewardsWithdrawn(address stakingProvider, uint96 amount)
```

Event emitted by `withdrawRewards` function.

### withdrawRewards

```solidity
function withdrawRewards(address stakingProvider) external
```

Withdraws application rewards for the given staking provider.
Rewards are withdrawn to the staking provider's beneficiary
address set in the staking contract.

Emits `RewardsWithdrawn` event.

### authorizationIncreased

```solidity
function authorizationIncreased(address stakingProvider, uint96 fromAmount, uint96 toAmount) external
```

Used by T staking contract to inform the application that the
authorized amount for the given staking provider increased.
The application may do any necessary housekeeping. The
application must revert the transaction in case the
authorization is below the minimum required.

### authorizationDecreaseRequested

```solidity
function authorizationDecreaseRequested(address stakingProvider, uint96 fromAmount, uint96 toAmount) external
```

Used by T staking contract to inform the application that the
authorization decrease for the given staking provider has been
requested. The application should mark the authorization as
pending decrease and respond to the staking contract with
`approveAuthorizationDecrease` at its discretion. It may
happen right away but it also may happen several months later.
If there is already a pending authorization decrease request
for the application, and the application does not agree for
overwriting it, the function should revert.

### involuntaryAuthorizationDecrease

```solidity
function involuntaryAuthorizationDecrease(address stakingProvider, uint96 fromAmount, uint96 toAmount) external
```

Used by T staking contract to inform the application the
authorization has been decreased for the given staking provider
involuntarily, as a result of slashing. Lets the application to
do any housekeeping neccessary. Called with 250k gas limit and
does not revert the transaction if
`involuntaryAuthorizationDecrease` call failed.

### availableRewards

```solidity
function availableRewards(address stakingProvider) external view returns (uint96)
```

Returns the amount of application rewards available for
withdrawal for the given staking provider.

### minimumAuthorization

```solidity
function minimumAuthorization() external view returns (uint96)
```

The minimum authorization amount required for the staking
provider so that they can participate in the application.

## IKeepTokenStaking

Interface for Keep TokenStaking contract

### seize

```solidity
function seize(uint256 amountToSeize, uint256 rewardMultiplier, address tattletale, address[] misbehavedOperators) external
```

Seize provided token amount from every member in the misbehaved
operators array. The tattletale is rewarded with 5% of the total seized
amount scaled by the reward adjustment parameter and the rest 95% is burned.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amountToSeize | uint256 | Token amount to seize from every misbehaved operator. |
| rewardMultiplier | uint256 | Reward adjustment in percentage. Min 1% and 100% max. |
| tattletale | address | Address to receive the 5% reward. |
| misbehavedOperators | address[] | Array of addresses to seize the tokens from. |

### getDelegationInfo

```solidity
function getDelegationInfo(address operator) external view returns (uint256 amount, uint256 createdAt, uint256 undelegatedAt)
```

Gets stake delegation info for the given operator.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| operator | address | Operator address. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | The amount of tokens the given operator delegated. |
| createdAt | uint256 | The time when the stake has been delegated. |
| undelegatedAt | uint256 | The time when undelegation has been requested. If undelegation has not been requested, 0 is returned. |

### ownerOf

```solidity
function ownerOf(address operator) external view returns (address)
```

Gets the stake owner for the specified operator address.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | address | Stake owner address. |

### beneficiaryOf

```solidity
function beneficiaryOf(address operator) external view returns (address payable)
```

Gets the beneficiary for the specified operator address.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | address payable | Beneficiary address. |

### authorizerOf

```solidity
function authorizerOf(address operator) external view returns (address)
```

Gets the authorizer for the specified operator address.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | address | Authorizer address. |

### eligibleStake

```solidity
function eligibleStake(address operator, address operatorContract) external view returns (uint256 balance)
```

Gets the eligible stake balance of the specified address.
An eligible stake is a stake that passed the initialization period
and is not currently undelegating. Also, the operator had to approve
the specified operator contract.

Operator with a minimum required amount of eligible stake can join the
network and participate in new work selection.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| operator | address | address of stake operator. |
| operatorContract | address | address of operator contract. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| balance | uint256 | an uint256 representing the eligible stake balance. |

## INuCypherStakingEscrow

Interface for NuCypher StakingEscrow contract

### slashStaker

```solidity
function slashStaker(address staker, uint256 penalty, address investigator, uint256 reward) external
```

Slash the staker's stake and reward the investigator

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| staker | address | Staker's address |
| penalty | uint256 | Penalty |
| investigator | address | Investigator |
| reward | uint256 | Reward for the investigator |

### requestMerge

```solidity
function requestMerge(address staker, address stakingProvider) external returns (uint256)
```

Request merge between NuCypher staking contract and T staking contract.
Returns amount of staked tokens

### getAllTokens

```solidity
function getAllTokens(address staker) external view returns (uint256)
```

Get all tokens belonging to the staker

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

## IManagedGrant

### grantee

```solidity
function grantee() external view returns (address)
```

## KeepStake

T network staking contract supports existing KEEP stakes by allowing
KEEP stakers to use their stakes in T network and weights them based
on KEEP<>T token ratio. KEEP stake owner is cached in T staking
contract and used to restrict access to all functions only owner or
operator should call. To cache KEEP stake owner in T staking
contract, T staking contract first needs to resolve the owner.

Resolving liquid KEEP stake owner is easy. Resolving token grant
stake owner is complicated and not possible to do on-chain from
a contract external to KEEP TokenStaking contract. Keep TokenStaking
knows the grant ID but does not expose it externally.

KeepStake contract addresses this problem by exposing
operator-owner mappings snapshotted off-chain based on events and
information publicly available from KEEP TokenStaking contract and
KEEP TokenGrant contract. Additionally, it gives the Governance
ability to add new mappings in case they are ever needed; in
practice, this will be needed only if someone decides to stake their
KEEP token grant in KEEP network after 2021-11-11 when the snapshot
was taken.

Operator-owner pairs were snapshotted 2021-11-11 in the following
way:
1. Fetch all TokenStaking events from KEEP staking contract.
2. Filter out undelegated operators.
3. Filter out canceled delegations.
4. Fetch grant stake information from KEEP TokenGrant for that
operator to determine if we are dealing with grant delegation.
5. Fetch grantee address from KEEP TokenGrant contract.
6. Check if we are dealing with ManagedGrant by looking for all
created ManagedGrants and comparing their address against grantee
address fetched from TokenGrant contract.

### keepTokenStaking

```solidity
contract IKeepTokenStaking keepTokenStaking
```

### operatorToManagedGrant

```solidity
mapping(address => address) operatorToManagedGrant
```

### operatorToGrantee

```solidity
mapping(address => address) operatorToGrantee
```

### constructor

```solidity
constructor(contract IKeepTokenStaking _keepTokenStaking) public
```

### setManagedGrant

```solidity
function setManagedGrant(address operator, address managedGrant) external
```

Allows the Governance to set new operator-managed grant pair.
This function should only be called for managed grants if
the snapshot does include this pair.

### setGrantee

```solidity
function setGrantee(address operator, address grantee) external
```

Allows the Governance to set new operator-grantee pair.
This function should only be called for non-managed grants if
the snapshot does include this pair.

### resolveOwner

```solidity
function resolveOwner(address operator) external view returns (address)
```

Resolves KEEP stake owner for the provided operator address.
Reverts if could not resolve the owner.

### resolveSnapshottedManagedGrantees

```solidity
function resolveSnapshottedManagedGrantees(address operator) internal view returns (address)
```

### resolveSnapshottedGrantees

```solidity
function resolveSnapshottedGrantees(address operator) internal pure returns (address)
```

## TokenStaking

TokenStaking is the main staking contract of the Threshold Network.
Apart from the basic usage of enabling T stakes, it also acts as a
sort of "meta-staking" contract, accepting existing legacy NU/KEEP
stakes. Additionally, it serves as application manager for the apps
that run on the Threshold Network. Note that legacy NU/KEEP staking
contracts see TokenStaking as an application (e.g., slashing is
requested by TokenStaking and performed by the legacy contracts).

TokenStaking is upgradeable, using OpenZeppelin's Upgradeability
framework. As such, it is required to satisfy OZ's guidelines, like
restrictions on constructors, immutable variables, base contracts and
libraries. See https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable

### ApplicationStatus

```solidity
enum ApplicationStatus {
  NOT_APPROVED,
  APPROVED,
  PAUSED,
  DISABLED
}
```

### StakingProviderInfo

```solidity
struct StakingProviderInfo {
  uint96 nuInTStake;
  address owner;
  uint96 keepInTStake;
  address payable beneficiary;
  uint96 tStake;
  address authorizer;
  mapping(address => struct TokenStaking.AppAuthorization) authorizations;
  address[] authorizedApplications;
  uint256 startStakingTimestamp;
}
```

### AppAuthorization

```solidity
struct AppAuthorization {
  uint96 authorized;
  uint96 deauthorizing;
}
```

### ApplicationInfo

```solidity
struct ApplicationInfo {
  enum TokenStaking.ApplicationStatus status;
  address panicButton;
}
```

### SlashingEvent

```solidity
struct SlashingEvent {
  address stakingProvider;
  uint96 amount;
}
```

### SLASHING_REWARD_PERCENT

```solidity
uint256 SLASHING_REWARD_PERCENT
```

### MIN_STAKE_TIME

```solidity
uint256 MIN_STAKE_TIME
```

### GAS_LIMIT_AUTHORIZATION_DECREASE

```solidity
uint256 GAS_LIMIT_AUTHORIZATION_DECREASE
```

### CONVERSION_DIVISOR

```solidity
uint256 CONVERSION_DIVISOR
```

### token

```solidity
contract T token
```

### keepStakingContract

```solidity
contract IKeepTokenStaking keepStakingContract
```

### keepStake

```solidity
contract KeepStake keepStake
```

### nucypherStakingContract

```solidity
contract INuCypherStakingEscrow nucypherStakingContract
```

### keepRatio

```solidity
uint256 keepRatio
```

### nucypherRatio

```solidity
uint256 nucypherRatio
```

### governance

```solidity
address governance
```

### minTStakeAmount

```solidity
uint96 minTStakeAmount
```

### authorizationCeiling

```solidity
uint256 authorizationCeiling
```

### stakeDiscrepancyPenalty

```solidity
uint96 stakeDiscrepancyPenalty
```

### stakeDiscrepancyRewardMultiplier

```solidity
uint256 stakeDiscrepancyRewardMultiplier
```

### notifiersTreasury

```solidity
uint256 notifiersTreasury
```

### notificationReward

```solidity
uint256 notificationReward
```

### stakingProviders

```solidity
mapping(address => struct TokenStaking.StakingProviderInfo) stakingProviders
```

### applicationInfo

```solidity
mapping(address => struct TokenStaking.ApplicationInfo) applicationInfo
```

### applications

```solidity
address[] applications
```

### slashingQueue

```solidity
struct TokenStaking.SlashingEvent[] slashingQueue
```

### slashingQueueIndex

```solidity
uint256 slashingQueueIndex
```

### Staked

```solidity
event Staked(enum IStaking.StakeType stakeType, address owner, address stakingProvider, address beneficiary, address authorizer, uint96 amount)
```

### MinimumStakeAmountSet

```solidity
event MinimumStakeAmountSet(uint96 amount)
```

### ApplicationStatusChanged

```solidity
event ApplicationStatusChanged(address application, enum TokenStaking.ApplicationStatus newStatus)
```

### AuthorizationIncreased

```solidity
event AuthorizationIncreased(address stakingProvider, address application, uint96 fromAmount, uint96 toAmount)
```

### AuthorizationDecreaseRequested

```solidity
event AuthorizationDecreaseRequested(address stakingProvider, address application, uint96 fromAmount, uint96 toAmount)
```

### AuthorizationDecreaseApproved

```solidity
event AuthorizationDecreaseApproved(address stakingProvider, address application, uint96 fromAmount, uint96 toAmount)
```

### AuthorizationInvoluntaryDecreased

```solidity
event AuthorizationInvoluntaryDecreased(address stakingProvider, address application, uint96 fromAmount, uint96 toAmount, bool successfulCall)
```

### PanicButtonSet

```solidity
event PanicButtonSet(address application, address panicButton)
```

### AuthorizationCeilingSet

```solidity
event AuthorizationCeilingSet(uint256 ceiling)
```

### ToppedUp

```solidity
event ToppedUp(address stakingProvider, uint96 amount)
```

### Unstaked

```solidity
event Unstaked(address stakingProvider, uint96 amount)
```

### TokensSeized

```solidity
event TokensSeized(address stakingProvider, uint96 amount, bool discrepancy)
```

### StakeDiscrepancyPenaltySet

```solidity
event StakeDiscrepancyPenaltySet(uint96 penalty, uint256 rewardMultiplier)
```

### NotificationRewardSet

```solidity
event NotificationRewardSet(uint96 reward)
```

### NotificationRewardPushed

```solidity
event NotificationRewardPushed(uint96 reward)
```

### NotificationRewardWithdrawn

```solidity
event NotificationRewardWithdrawn(address recipient, uint96 amount)
```

### NotifierRewarded

```solidity
event NotifierRewarded(address notifier, uint256 amount)
```

### SlashingProcessed

```solidity
event SlashingProcessed(address caller, uint256 count, uint256 tAmount)
```

### OwnerRefreshed

```solidity
event OwnerRefreshed(address stakingProvider, address oldOwner, address newOwner)
```

### GovernanceTransferred

```solidity
event GovernanceTransferred(address oldGovernance, address newGovernance)
```

### onlyGovernance

```solidity
modifier onlyGovernance()
```

### onlyPanicButtonOf

```solidity
modifier onlyPanicButtonOf(address application)
```

### onlyAuthorizerOf

```solidity
modifier onlyAuthorizerOf(address stakingProvider)
```

### onlyOwnerOrStakingProvider

```solidity
modifier onlyOwnerOrStakingProvider(address stakingProvider)
```

### onlyOwnerOf

```solidity
modifier onlyOwnerOf(address stakingProvider)
```

### constructor

```solidity
constructor(contract T _token, contract IKeepTokenStaking _keepStakingContract, contract INuCypherStakingEscrow _nucypherStakingContract, contract VendingMachine _keepVendingMachine, contract VendingMachine _nucypherVendingMachine, contract KeepStake _keepStake) public
```

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _token | contract T | Address of T token contract |
| _keepStakingContract | contract IKeepTokenStaking | Address of Keep staking contract |
| _nucypherStakingContract | contract INuCypherStakingEscrow | Address of NuCypher staking contract |
| _keepVendingMachine | contract VendingMachine | Address of Keep vending machine |
| _nucypherVendingMachine | contract VendingMachine | Address of NuCypher vending machine |
| _keepStake | contract KeepStake | Address of Keep contract with grant owners |

### initialize

```solidity
function initialize() external
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
staking contract, additionally appointing beneficiary and
authorizer roles. Caches the amount staked in NU staking
contract. Can be called only by the original delegation owner.

### setMinimumStakeAmount

```solidity
function setMinimumStakeAmount(uint96 amount) external
```

Allows the Governance to set the minimum required stake amount.
This amount is required to protect against griefing the staking
contract and individual applications are allowed to require
higher minimum stakes if necessary.

Staking providers are not required to maintain a minimum T stake
all the time. 24 hours after the delegation, T stake can be reduced
below the minimum stake. The minimum stake in the staking contract
is just to protect against griefing stake operation. Please note
that each application may have its own minimum authorization though
and the authorization can not be higher than the stake.

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
the given staking provider’s authorizer.

Calls `authorizationIncreased` callback on the given application to
notify the application about authorization change.
See `IApplication`.

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

Calls `authorizationDecreaseRequested` callback
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
`forceDecreaseAuthorization` at any moment. Can be called only
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
the liquid T stake amount. Can be called only by the owner or
the staking provider. Can only be called when 24h passed since
the stake has been delegated.

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
Can only be called when 24h passed since the stake has been
delegated.

This function (or `unstakeAll`) must be called before
`undelegate`/`undelegateAt` in Keep staking contract. Otherwise
provider can be slashed by `notifyKeepStakeDiscrepancy` method.

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
by the delegation owner or the staking provider. Can only be
called when 24h passed since the stake has been delegated.

This function (or `unstakeAll`) must be called before `withdraw`
in NuCypher staking contract. Otherwise NU tokens can't be
unlocked.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| stakingProvider | address | Staking provider address |
| amount | uint96 | Amount of NU to unstake in T denomination |

### unstakeAll

```solidity
function unstakeAll(address stakingProvider) external
```

Sets cached legacy stake amount to 0, sets the liquid T stake
amount to 0 and withdraws all liquid T from the stake to the
owner. Reverts if there is at least one non-zero authorization.
Can be called only by the delegation owner or the staking
provider. Can only be called when 24h passed since the stake
has been delegated.

### notifyKeepStakeDiscrepancy

```solidity
function notifyKeepStakeDiscrepancy(address stakingProvider) external
```

Notifies about the discrepancy between legacy KEEP active stake
and the amount cached in T staking contract. Slashes the staking
provider in case the amount cached is higher than the actual
active stake amount in KEEP staking contract. Needs to update
authorizations of all affected applications and execute an
involuntary authorization decrease on all affected applications.
Can be called by anyone, notifier receives a reward.

### notifyNuStakeDiscrepancy

```solidity
function notifyNuStakeDiscrepancy(address stakingProvider) external
```

Notifies about the discrepancy between legacy NU active stake
and the amount cached in T staking contract. Slashes the
staking provider in case the amount cached is higher than the
actual active stake amount in NU staking contract. Needs to
update authorizations of all affected applications and execute an
involuntary authorization decrease on all affected applications.
Can be called by anyone, notifier receives a reward.

Real discrepancy between T and Nu is impossible.
This method is a safeguard in case of bugs in NuCypher staking
contract

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
function slash(uint96 amount, address[] _stakingProviders) external
```

Adds staking providers to the slashing queue along with the
amount that should be slashed from each one of them. Can only be
called by application authorized for all staking providers in
the array.

This method doesn't emit events for providers that are added to
the queue. If necessary  events can be added to the application
level.

### seize

```solidity
function seize(uint96 amount, uint256 rewardMultiplier, address notifier, address[] _stakingProviders) external
```

Adds staking providers to the slashing queue along with the
amount. The notifier will receive reward per each provider from
notifiers treasury. Can only be called by application
authorized for all staking providers in the array.

This method doesn't emit events for staking providers that are
added to the queue. If necessary  events can be added to the
application level.

### processSlashing

```solidity
function processSlashing(uint256 count) external virtual
```

Takes the given number of queued slashing operations and
processes them. Receives 5% of the slashed amount.
Executes `involuntaryAuthorizationDecrease` function on each
affected application.

### delegateVoting

```solidity
function delegateVoting(address stakingProvider, address delegatee) external
```

Delegate voting power from the stake associated to the
`stakingProvider` to a `delegatee` address. Caller must be the
owner of this stake.

### transferGovernance

```solidity
function transferGovernance(address newGuvnor) external virtual
```

Transfers ownership of the contract to `newGuvnor`.

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
function stakedNu(address stakingProvider) external view returns (uint256 nuAmount)
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

### requestAuthorizationDecrease

```solidity
function requestAuthorizationDecrease(address stakingProvider, address application, uint96 amount) public
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

Calls `authorizationDecreaseRequested` callback on the given
application. See `IApplication`.

### getMinStaked

```solidity
function getMinStaked(address stakingProvider, enum IStaking.StakeType stakeTypes) public view returns (uint96)
```

Returns minimum possible stake for T, KEEP or NU in T denomination

For example, suppose the given staking provider has 10 T, 20 T worth
of KEEP, and 30 T worth of NU all staked, and the maximum
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
needed to satisfy the maximum application authorization given
the staked amounts of the other stake types for that staking
provider.

### getAvailableToAuthorize

```solidity
function getAvailableToAuthorize(address stakingProvider, address application) public view returns (uint96 availableTValue)
```

Returns available amount to authorize for the specified
application.

### delegate

```solidity
function delegate(address stakingProvider, address delegatee) internal virtual
```

Delegate voting power from the stake associated to the
`stakingProvider` to a `delegatee` address. Caller must be the owner
of this stake.

Original abstract function defined in Checkpoints contract had two
parameters, `delegator` and `delegatee`. Here we override it and
comply with the same signature but the semantics of the first
parameter changes to the `stakingProvider` address.

### notify

```solidity
function notify(uint96 amount, uint256 rewardMultiplier, address notifier, address[] _stakingProviders) internal
```

Adds staking providers to the slashing queue along with the
amount. The notifier will receive reward per each staking
provider from notifiers treasury. Can only be called by
application authorized for all staking providers in the array.

### processSlashing

```solidity
function processSlashing(struct TokenStaking.SlashingEvent slashing) internal returns (uint96 tAmountToBurn)
```

Processes one specified slashing event.
Executes `involuntaryAuthorizationDecrease` function on each
affected application.

### authorizationDecrease

```solidity
function authorizationDecrease(address stakingProvider, struct TokenStaking.StakingProviderInfo stakingProviderStruct, uint96 slashedAmount) internal
```

Synchronize authorizations (if needed) after slashing stake

### seizeKeep

```solidity
function seizeKeep(struct TokenStaking.StakingProviderInfo stakingProviderStruct, address stakingProvider, uint96 tAmountToSlash, uint256 rewardMultiplier) internal returns (uint96)
```

Convert amount from T to Keep and call `seize` in Keep staking contract.
Returns remainder of slashing amount in T

Note this internal function doesn't update stake checkpoints

### seizeNu

```solidity
function seizeNu(struct TokenStaking.StakingProviderInfo stakingProviderStruct, uint96 tAmountToSlash, uint256 rewardMultiplier) internal returns (uint96)
```

Convert amount from T to NU and call `slashStaker` in NuCypher staking contract.
Returns remainder of slashing amount in T

Note this internal function doesn't update the stake checkpoints

### cleanAuthorizedApplications

```solidity
function cleanAuthorizedApplications(struct TokenStaking.StakingProviderInfo stakingProviderStruct, uint256 numberToDelete) internal
```

Removes application with zero authorization from authorized
applications array

### newStakeCheckpoint

```solidity
function newStakeCheckpoint(address _delegator, uint96 _amount, bool increase) internal
```

Creates new checkpoints due to a change of stake amount

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _delegator | address | Address of the staking provider acting as delegator |
| _amount | uint96 | Amount of T to increment |
| increase | bool | True if the change is an increase, false if a decrease |

### increaseStakeCheckpoint

```solidity
function increaseStakeCheckpoint(address _delegator, uint96 _amount) internal
```

Creates new checkpoints due to an increment of a stakers' stake

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _delegator | address | Address of the staking provider acting as delegator |
| _amount | uint96 | Amount of T to increment |

### decreaseStakeCheckpoint

```solidity
function decreaseStakeCheckpoint(address _delegator, uint96 _amount) internal
```

Creates new checkpoints due to a decrease of a stakers' stake

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _delegator | address | Address of the stake owner acting as delegator |
| _amount | uint96 | Amount of T to decrease |

### getNuAmountInT

```solidity
function getNuAmountInT(address owner, address stakingProvider) internal returns (uint96)
```

Returns amount of Nu stake in the NuCypher staking contract for
the specified staking provider.
Resulting value in T denomination

### _transferGovernance

```solidity
function _transferGovernance(address newGuvnor) internal virtual
```

### getKeepAmountInT

```solidity
function getKeepAmountInT(address stakingProvider) internal view returns (uint96)
```

Returns amount of Keep stake in the Keep staking contract for
the specified staking provider.
Resulting value in T denomination

### convertToT

```solidity
function convertToT(uint256 amount, uint256 ratio) internal pure returns (uint96 tAmount, uint256 remainder)
```

Returns the T token amount that's obtained from `amount` legacy
tokens for the given `ratio`, and the remainder that can't be
converted.

### convertFromT

```solidity
function convertFromT(uint96 tAmount, uint256 ratio) internal pure returns (uint256 amount, uint96 tRemainder)
```

Returns the amount of legacy tokens that's obtained from
`tAmount` T tokens for the given `ratio`, and the T remainder
that can't be converted.

## T

Threshold Network T token

By default, token balance does not account for voting power.
This makes transfers cheaper. The downside is that it requires users
to delegate to themselves to activate checkpoints and have their
voting power tracked.

### DELEGATION_TYPEHASH

```solidity
bytes32 DELEGATION_TYPEHASH
```

The EIP-712 typehash for the delegation struct used by
`delegateBySig`.

### constructor

```solidity
constructor() public
```

### delegateBySig

```solidity
function delegateBySig(address signatory, address delegatee, uint256 deadline, uint8 v, bytes32 r, bytes32 s) external
```

Delegates votes from signatory to `delegatee`

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| signatory | address |  |
| delegatee | address | The address to delegate votes to |
| deadline | uint256 | The time at which to expire the signature |
| v | uint8 | The recovery byte of the signature |
| r | bytes32 | Half of the ECDSA signature pair |
| s | bytes32 | Half of the ECDSA signature pair |

### delegate

```solidity
function delegate(address delegatee) public virtual
```

Delegate votes from `msg.sender` to `delegatee`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| delegatee | address | The address to delegate votes to |

### beforeTokenTransfer

```solidity
function beforeTokenTransfer(address from, address to, uint256 amount) internal
```

Hook that is called before any transfer of tokens. This includes
     minting and burning.

Calling conditions:
- when `from` and `to` are both non-zero, `amount` of `from`'s tokens
  will be to transferred to `to`.
- when `from` is zero, `amount` tokens will be minted for `to`.
- when `to` is zero, `amount` of ``from``'s tokens will be burned.
- `from` and `to` are never both zero.

### delegate

```solidity
function delegate(address delegator, address delegatee) internal virtual
```

Change delegation for `delegator` to `delegatee`.

## PercentUtils

### percent

```solidity
function percent(uint256 a, uint256 b) internal pure returns (uint256)
```

### asPercentOf

```solidity
function asPercentOf(uint256 a, uint256 b) internal pure returns (uint256)
```

## SafeTUpgradeable

A wrapper around OpenZeppelin's `SafeERC20Upgradeable` but specific
to the T token. Use this library in upgradeable contracts. If your
contract is non-upgradeable, then the traditional `SafeERC20` works.
The motivation is to prevent upgradeable contracts that use T from
depending on the `Address` library, which can be problematic since
it uses `delegatecall`, which is discouraged by OpenZeppelin for use
in upgradeable contracts.

This implementation force-casts T to `IERC20Upgradeable` to make it
work with `SafeERC20Upgradeable`.

### safeTransfer

```solidity
function safeTransfer(contract T token, address to, uint256 value) internal
```

### safeTransferFrom

```solidity
function safeTransferFrom(contract T token, address from, address to, uint256 value) internal
```

## VendingMachine

Contract implements a special update protocol to enable KEEP/NU
token holders to wrap their tokens and obtain T tokens according
to a fixed ratio. This will go on indefinitely and enable NU and
KEEP token holders to join T network without needing to buy or
sell any assets. Logistically, anyone holding NU or KEEP can wrap
those assets in order to upgrade to T. They can also unwrap T in
order to downgrade back to the underlying asset. There is a separate
instance of this contract deployed for KEEP holders and a separate
instance of this contract deployed for NU holders.

### WRAPPED_TOKEN_CONVERSION_PRECISION

```solidity
uint256 WRAPPED_TOKEN_CONVERSION_PRECISION
```

Number of decimal places of precision in conversion to/from
wrapped tokens (assuming typical ERC20 token with 18 decimals).
This implies that amounts of wrapped tokens below this precision
won't take part in the conversion. E.g., for a value of 3, then
for a conversion of 1.123456789 wrapped tokens, only 1.123 is
convertible (i.e., 3 decimal places), and 0.000456789 is left.

### FLOATING_POINT_DIVISOR

```solidity
uint256 FLOATING_POINT_DIVISOR
```

Divisor for precision purposes, used to represent fractions.

### wrappedToken

```solidity
contract IERC20 wrappedToken
```

The token being wrapped to T (KEEP/NU).

### tToken

```solidity
contract T tToken
```

T token contract.

### ratio

```solidity
uint256 ratio
```

The ratio with which T token is converted based on the provided
token being wrapped (KEEP/NU), expressed in 1e18 precision.

When wrapping:
x [T] = amount [KEEP/NU] * ratio / FLOATING_POINT_DIVISOR

When unwrapping:
x [KEEP/NU] = amount [T] * FLOATING_POINT_DIVISOR / ratio

### wrappedBalance

```solidity
mapping(address => uint256) wrappedBalance
```

The total balance of wrapped tokens for the given holder
account. Only holders that have previously wrapped KEEP/NU to T
can unwrap, up to the amount previously wrapped.

### Wrapped

```solidity
event Wrapped(address recipient, uint256 wrappedTokenAmount, uint256 tTokenAmount)
```

### Unwrapped

```solidity
event Unwrapped(address recipient, uint256 tTokenAmount, uint256 wrappedTokenAmount)
```

### constructor

```solidity
constructor(contract IERC20 _wrappedToken, contract T _tToken, uint96 _wrappedTokenAllocation, uint96 _tTokenAllocation) public
```

Sets the reference to `wrappedToken` and `tToken`. Initializes
conversion `ratio` between wrapped token and T based on the
provided `_tTokenAllocation` and `_wrappedTokenAllocation`.

Multiplications in this contract can't overflow uint256 as we
restrict `_wrappedTokenAllocation` and `_tTokenAllocation` to
96 bits and FLOATING_POINT_DIVISOR fits in less than 60 bits.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _wrappedToken | contract IERC20 | Address to ERC20 token that will be wrapped to T |
| _tToken | contract T | Address of T token |
| _wrappedTokenAllocation | uint96 | The total supply of the token that will be wrapped to T |
| _tTokenAllocation | uint96 | The allocation of T this instance of Vending Machine will receive |

### wrap

```solidity
function wrap(uint256 amount) external
```

Wraps up to the the given `amount` of the token (KEEP/NU) and
releases T token proportionally to the amount being wrapped with
respect to the wrap ratio. The token holder needs to have at
least the given amount of the wrapped token (KEEP/NU) approved
to transfer to the Vending Machine before calling this function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | The amount of KEEP/NU to be wrapped |

### receiveApproval

```solidity
function receiveApproval(address from, uint256 amount, address token, bytes) external
```

Wraps up to the given amount of the token (KEEP/NU) and releases
T token proportionally to the amount being wrapped with respect
to the wrap ratio. This is a shortcut to `wrap` function that
avoids a separate approval transaction. Only KEEP/NU token
is allowed as a caller, so please call this function via
token's `approveAndCall`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| from | address | Caller's address, must be the same as `wrappedToken` field |
| amount | uint256 | The amount of KEEP/NU to be wrapped |
| token | address | Token's address, must be the same as `wrappedToken` field |
|  | bytes |  |

### unwrap

```solidity
function unwrap(uint256 amount) external
```

Unwraps up to the given `amount` of T back to the legacy token
(KEEP/NU) according to the wrap ratio. It can only be called by
a token holder who previously wrapped their tokens in this
vending machine contract. The token holder can't unwrap more
tokens than they originally wrapped. The token holder needs to
have at least the given amount of T tokens approved to transfer
to the Vending Machine before calling this function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | The amount of T to unwrap back to the collateral (KEEP/NU) |

### conversionToT

```solidity
function conversionToT(uint256 amount) public view returns (uint256 tAmount, uint256 wrappedRemainder)
```

Returns the T token amount that's obtained from `amount` wrapped
tokens (KEEP/NU), and the remainder that can't be upgraded.

### conversionFromT

```solidity
function conversionFromT(uint256 amount) public view returns (uint256 wrappedAmount, uint256 tRemainder)
```

The amount of wrapped tokens (KEEP/NU) that's obtained from
`amount` T tokens, and the remainder that can't be downgraded.

### _wrap

```solidity
function _wrap(address tokenHolder, uint256 wrappedTokenAmount) internal
```

### _unwrap

```solidity
function _unwrap(address tokenHolder, uint256 tTokenAmount) internal
```

