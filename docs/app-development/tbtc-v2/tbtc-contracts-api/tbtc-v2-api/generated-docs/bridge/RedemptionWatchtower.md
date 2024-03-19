# Solidity API

## RedemptionWatchtower

This contract encapsulates the logic behind the redemption veto
mechanism of the Bridge. The redemption veto mechanism is a safeguard
in the event of malicious redemption requests such as those sourced
from a Bridge hack. The mechanism involves a permissioned set of
Guardians that can object to a redemption request. If a redemption
is objected to by a redemption guardian, it is delayed. Two
subsequent objections from redemption guardians results in a veto
of the redemption request. A veto returns redeemed amount to the
requester while inflicting a freeze and financial penalty on the
amount. The goal of this penalty is to introduce a cost that guards
against repeated malicious redemption requests.

### VetoProposal

```solidity
struct VetoProposal {
  address redeemer;
  uint64 withdrawableAmount;
  uint32 finalizedAt;
  uint8 objectionsCount;
}
```

### REQUIRED_OBJECTIONS_COUNT

```solidity
uint8 REQUIRED_OBJECTIONS_COUNT
```

Number of guardian objections required to veto a redemption request.

### isGuardian

```solidity
mapping(address => bool) isGuardian
```

Set of redemption guardians.

### isBanned

```solidity
mapping(address => bool) isBanned
```

Set of banned redeemer addresses. Banned redeemers cannot
request redemptions. A redeemer is banned if one of their
redemption requests is vetoed due to an enough number of
guardian objections.

### vetoProposals

```solidity
mapping(uint256 => struct RedemptionWatchtower.VetoProposal) vetoProposals
```

Set of veto proposals indexed by the redemption key built as
`keccak256(keccak256(redeemerOutputScript) | walletPubKeyHash)`.
The `walletPubKeyHash` is the 20-byte wallet's public key hash
(computed using Bitcoin HASH160 over the compressed ECDSA
public key) and `redeemerOutputScript` is the Bitcoin script
(P2PKH, P2WPKH, P2SH or P2WSH) that is involved in the
redemption request.

### objections

```solidity
mapping(uint256 => bool) objections
```

Set of individual guardian objections indexed by the objection
key built as `keccak256(redemptionKey | guardian)`.
The `redemptionKey` is the redemption key built in the same way
as in the `vetoProposals` mapping. The `guardian` is the
address of the  guardian who raised the objection.

### bridge

```solidity
contract Bridge bridge
```

The Bridge contract.

### watchtowerEnabledAt

```solidity
uint32 watchtowerEnabledAt
```

UNIX timestamp the redemption watchtower (and veto mechanism)
was enabled at.

### watchtowerLifetime

```solidity
uint32 watchtowerLifetime
```

Duration of the watchtower lifetime in seconds. Once this
period elapses (since the `watchtowerEnabledAt` timestamp),
the watchtower can be permanently disabled by anyone.

### watchtowerDisabledAt

```solidity
uint32 watchtowerDisabledAt
```

UNIX timestamp the redemption watchtower (and veto mechanism)
was permanently disabled at.

### manager

```solidity
address manager
```

Address of the manager responsible for parameters management.

### vetoPenaltyFeeDivisor

```solidity
uint64 vetoPenaltyFeeDivisor
```

Divisor used to compute the redemption veto penalty fee deducted
upon veto finalization. This fee diminishes the amount that the
redeemer can claim after the freeze period and is computed as follows:
`penaltyFee = requestedAmount / redemptionVetoPenaltyFeeDivisor`
For example, if the penalty fee needs to be 2% of each vetoed
redemption request, the `redemptionVetoPenaltyFeeDivisor` should
be set to `50` because `1/50 = 0.02 = 2%`.

### vetoFreezePeriod

```solidity
uint32 vetoFreezePeriod
```

Time of the redemption veto freeze period. It is the time after
which the redeemer can claim the amount of the vetoed redemption
request. The freeze period is counted from the moment when the
veto request was finalized (i.e. moment of the last guardian
objection that caused finalization). Value in seconds.

### bank

```solidity
contract Bank bank
```

The Bank contract.

### defaultDelay

```solidity
uint32 defaultDelay
```

Default delay applied to each redemption request. It is the time
during which redemption guardians can raise the first objection.
Wallets are not allowed to finalize the redemption request before
the delay is over. The delay is counted from the moment when the
redemption request was created. Value in seconds.

### levelOneDelay

```solidity
uint32 levelOneDelay
```

Delay applied to redemption requests a single guardian raised an
objection to. It is the time during which the remaining guardians
can raise their objections. Wallets are not allowed to finalize the
redemption request before the delay is over. The delay is counted
from the moment when the redemption request was created.
Value in seconds.

### levelTwoDelay

```solidity
uint32 levelTwoDelay
```

Delay applied to redemption requests two guardians raised an
objection to. It is the time during which the last guardian
can raise its objection. Wallets are not allowed to finalize the
redemption request before the delay is over. The delay is counted
from the moment when the redemption request was created.
Value in seconds.

### waivedAmountLimit

```solidity
uint64 waivedAmountLimit
```

Limit of the redemption amount that can be waived from the
redemption veto mechanism. Redemption requests with the requested
amount lesser than this limit can be processed immediately and
cannot be subject of guardian objections. Value in satoshis.

### WatchtowerEnabled

```solidity
event WatchtowerEnabled(uint32 enabledAt, address manager)
```

### WatchtowerDisabled

```solidity
event WatchtowerDisabled(uint32 disabledAt, address executor)
```

### GuardianAdded

```solidity
event GuardianAdded(address guardian)
```

### GuardianRemoved

```solidity
event GuardianRemoved(address guardian)
```

### ObjectionRaised

```solidity
event ObjectionRaised(uint256 redemptionKey, address guardian)
```

### VetoPeriodCheckOmitted

```solidity
event VetoPeriodCheckOmitted(uint256 redemptionKey)
```

### VetoFinalized

```solidity
event VetoFinalized(uint256 redemptionKey)
```

### WatchtowerParametersUpdated

```solidity
event WatchtowerParametersUpdated(uint32 watchtowerLifetime, uint64 vetoPenaltyFeeDivisor, uint32 vetoFreezePeriod, uint32 defaultDelay, uint32 levelOneDelay, uint32 levelTwoDelay, uint64 waivedAmountLimit)
```

### Banned

```solidity
event Banned(address redeemer)
```

### Unbanned

```solidity
event Unbanned(address redeemer)
```

### VetoedFundsWithdrawn

```solidity
event VetoedFundsWithdrawn(uint256 redemptionKey, address redeemer, uint64 amount)
```

### onlyManager

```solidity
modifier onlyManager()
```

### onlyGuardian

```solidity
modifier onlyGuardian()
```

### constructor

```solidity
constructor() public
```

### initialize

```solidity
function initialize(contract Bridge _bridge) external
```

### enableWatchtower

```solidity
function enableWatchtower(address _manager, address[] _guardians) external
```

Enables the redemption watchtower and veto mechanism.

Requirements:
- The caller must be the owner,
- Watchtower must not be enabled already,
- Manager address must not be zero.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _manager | address | Address of the watchtower manager. |
| _guardians | address[] | List of initial guardian addresses. |

### disableWatchtower

```solidity
function disableWatchtower() external
```

Disables the redemption watchtower and veto mechanism.
Can be called by anyone.

Requirements:
- Watchtower must be enabled,
- Watchtower must not be disabled already,
- Watchtower lifetime must have expired.

### addGuardian

```solidity
function addGuardian(address guardian) external
```

Adds a redemption guardian

Requirements:
- The caller must be the watchtower manager,
- The guardian must not already exist.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| guardian | address | Address of the guardian to add. |

### _addGuardian

```solidity
function _addGuardian(address guardian) internal
```

Adds a redemption guardian

Requirements:
- The guardian must not already exist.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| guardian | address | Address of the guardian to add. |

### removeGuardian

```solidity
function removeGuardian(address guardian) external
```

Removes a redemption guardian

Requirements:
- The caller must be the owner,
- The guardian must exist.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| guardian | address | Address of the guardian to remove. |

### raiseObjection

```solidity
function raiseObjection(bytes20 walletPubKeyHash, bytes redeemerOutputScript) external
```

Raises an objection to a redemption request identified by the
key built as `keccak256(keccak256(redeemerOutputScript) | walletPubKeyHash)`.
Each redemption has a default delay period during which
the wallet is not allowed to process it and the guardians
can raise objections to. Each objection extends the delay
period by a certain amount of time. The third objection
vetoes the redemption request. This causes the redemption
request to be rejected and the redeemer to be penalized.
Specific consequences of a veto are as follows:
- The redemption amount is frozen for a certain period of time,
- Once the freeze period expires, the redeemer can claim the
frozen amount minus a penalty fee,
- The penalty fee is burned,
- The redeemer is banned from making future redemption requests.

Requirements:
- The caller must be a redemption guardian,
- The redemption request must not have been vetoed already,
- The guardian must not have already objected to the redemption request,
- The redemption request must exist (i.e. must be pending),
- The redemption request must be within the optimistic redemption
delay period. The only exception is when the redemption request
was created before the optimistic redemption mechanism
initialization timestamp. In this case, the redemption request
can be objected to without any time restrictions.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |
| redeemerOutputScript | bytes | The redeemer's length-prefixed output script (P2PKH, P2WPKH, P2SH or P2WSH). |

### _redemptionDelay

```solidity
function _redemptionDelay(uint8 objectionsCount, uint64 requestedAmount) internal view returns (uint32)
```

Returns the redemption delay for a given number of objections
and requested amount.

If the watchtower has been disabled, the delay is always zero,
for any redemption request.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| objectionsCount | uint8 | Number of objections. |
| requestedAmount | uint64 | Requested redemption amount. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint32 | Redemption delay. |

### getRedemptionDelay

```solidity
function getRedemptionDelay(uint256 redemptionKey) external view returns (uint32)
```

Returns the applicable redemption delay for a redemption
request identified by the given redemption key.

If the watchtower has been disabled, the delay is always zero,
for any redemption request.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| redemptionKey | uint256 | Redemption key built as `keccak256(keccak256(redeemerOutputScript) | walletPubKeyHash)`. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint32 | Redemption delay. |

### updateWatchtowerParameters

```solidity
function updateWatchtowerParameters(uint32 _watchtowerLifetime, uint64 _vetoPenaltyFeeDivisor, uint32 _vetoFreezePeriod, uint32 _defaultDelay, uint32 _levelOneDelay, uint32 _levelTwoDelay, uint64 _waivedAmountLimit) external
```

Updates the watchtower parameters.

Requirements:
- The caller must be the watchtower manager,
- The new watchtower lifetime must not be lesser than the current one,
- The new redemption veto penalty fee divisor must be in range [0%, 5%],
- The new redemption level-two delay must not be lesser than level-one delay,
- The new redemption level-one delay must not be lesser than default delay.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _watchtowerLifetime | uint32 | Duration of the watchtower lifetime in seconds. |
| _vetoPenaltyFeeDivisor | uint64 | Divisor used to compute the redemption veto penalty fee deducted upon veto finalization. |
| _vetoFreezePeriod | uint32 | Time of the redemption veto freeze period. |
| _defaultDelay | uint32 | Default delay applied to each redemption request. |
| _levelOneDelay | uint32 | Delay applied to redemption requests a single guardian raised an objection to. |
| _levelTwoDelay | uint32 | Delay applied to redemption requests two guardians raised an objection to. |
| _waivedAmountLimit | uint64 | Limit of the redemption amount that can be waived from the redemption veto mechanism. |

### isSafeRedemption

```solidity
function isSafeRedemption(bytes20 walletPubKeyHash, bytes redeemerOutputScript, address balanceOwner, address redeemer) external view returns (bool)
```

Determines whether a redemption request is considered safe.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet that is meant to handle the redemption request. |
| redeemerOutputScript | bytes | The redeemer's length-prefixed output script (P2PKH, P2WPKH, P2SH or P2WSH) that is meant to receive the redeemed amount. |
| balanceOwner | address | The address of the Bank balance owner whose balance is getting redeemed. |
| redeemer | address | The address that requested the redemption. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if the redemption request is safe, false otherwise. The redemption is considered safe when: - The balance owner is not banned, - The redeemer is not banned, - There are no objections against past redemptions from the given wallet to the given redeemer output script. |

### unban

```solidity
function unban(address redeemer) external
```

Unbans a redeemer.

Requirements:
- The caller must be the watchtower manager,
- The redeemer must be banned.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| redeemer | address | Address of the redeemer to unban. |

### withdrawVetoedFunds

```solidity
function withdrawVetoedFunds(uint256 redemptionKey) external
```

Withdraws funds from a vetoed redemption request, identified
by the given redemption key.

Requirements:
- The veto must be finalized,
- The caller must be the redeemer of the vetoed redemption request,
- The freeze period must have expired,
- There must be funds to withdraw.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| redemptionKey | uint256 | Redemption key built as `keccak256(keccak256(redeemerOutputScript) | walletPubKeyHash)`. |

