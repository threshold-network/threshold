# Solidity API

## BeaconDkg

{% hint style="warning" %}
This file documents a contract which is not yet deployed to Mainnet.
{% endhint %}

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

