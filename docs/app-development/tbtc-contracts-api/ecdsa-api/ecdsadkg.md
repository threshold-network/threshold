# EcdsaDkg

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

Initializes SortitionPool and EcdsaDkgValidator addresses. Can be performed only once.

#### Parameters

| Name            | Type                       | Description                 |
| --------------- | -------------------------- | --------------------------- |
| self            | struct EcdsaDkg.Data       |                             |
| \_sortitionPool | contract SortitionPool     | Sortition Pool reference    |
| \_dkgValidator  | contract EcdsaDkgValidator | EcdsaDkgValidator reference |

### currentState

```solidity
function currentState(struct EcdsaDkg.Data self) internal view returns (enum EcdsaDkg.State state)
```

Determines the current state of group creation. It doesn't take timeouts into consideration. The timeouts should be tracked and notified separately.

### lockState

```solidity
function lockState(struct EcdsaDkg.Data self) internal
```

Locks the sortition pool and starts awaiting for the group creation seed.

### start

```solidity
function start(struct EcdsaDkg.Data self, uint256 seed) internal
```

### submitResult

```solidity
function submitResult(struct EcdsaDkg.Data self, struct EcdsaDkg.Result result) internal
```

Allows to submit a DKG result. The submitted result does not go through a validation and before it gets accepted, it needs to wait through the challenge period during which everyone has a chance to challenge the result as invalid one. Submitter of the result needs to be in the sortition pool and if the result gets challenged, the submitter will get slashed.

### hasSeedTimedOut

```solidity
function hasSeedTimedOut(struct EcdsaDkg.Data self) internal view returns (bool)
```

Checks if awaiting seed timed out.

#### Return Values

| Name | Type | Description                                       |
| ---- | ---- | ------------------------------------------------- |
| \[0] | bool | True if awaiting seed timed out, false otherwise. |

### hasDkgTimedOut

```solidity
function hasDkgTimedOut(struct EcdsaDkg.Data self) internal view returns (bool)
```

Checks if DKG timed out. The DKG timeout period includes time required for off-chain protocol execution and time for the result publication. After this time a result cannot be submitted and DKG can be notified about the timeout. DKG period is adjusted by result submission offset that include blocks that were mined while invalid result has been registered until it got challenged.

#### Return Values

| Name | Type | Description                             |
| ---- | ---- | --------------------------------------- |
| \[0] | bool | True if DKG timed out, false otherwise. |

### notifySeedTimeout

```solidity
function notifySeedTimeout(struct EcdsaDkg.Data self) internal
```

Notifies about the seed was not delivered and restores the initial DKG state (IDLE).

### notifyDkgTimeout

```solidity
function notifyDkgTimeout(struct EcdsaDkg.Data self) internal
```

Notifies about DKG timeout.

### approveResult

```solidity
function approveResult(struct EcdsaDkg.Data self, struct EcdsaDkg.Result result) internal returns (uint32[] misbehavedMembers)
```

Approves DKG result. Can be called when the challenge period for the submitted result is finished. Considers the submitted result as valid. For the first `submitterPrecedencePeriodLength` blocks after the end of the challenge period can be called only by the DKG result submitter. After that time, can be called by anyone.

Can be called after a challenge period for the submitted result.

#### Parameters

| Name   | Type                   | Description                                                                      |
| ------ | ---------------------- | -------------------------------------------------------------------------------- |
| self   | struct EcdsaDkg.Data   |                                                                                  |
| result | struct EcdsaDkg.Result | Result to approve. Must match the submitted result stored during `submitResult`. |

#### Return Values

| Name              | Type      | Description                                       |
| ----------------- | --------- | ------------------------------------------------- |
| misbehavedMembers | uint32\[] | Identifiers of members who misbehaved during DKG. |

### challengeResult

```solidity
function challengeResult(struct EcdsaDkg.Data self, struct EcdsaDkg.Result result) internal returns (bytes32 maliciousResultHash, uint32 maliciousSubmitter)
```

Challenges DKG result. If the submitted result is proved to be invalid it reverts the DKG back to the result submission phase.

Can be called during a challenge period for the submitted result.

#### Parameters

| Name   | Type                   | Description                                                                        |
| ------ | ---------------------- | ---------------------------------------------------------------------------------- |
| self   | struct EcdsaDkg.Data   |                                                                                    |
| result | struct EcdsaDkg.Result | Result to challenge. Must match the submitted result stored during `submitResult`. |

#### Return Values

| Name                | Type    | Description                            |
| ------------------- | ------- | -------------------------------------- |
| maliciousResultHash | bytes32 | Hash of the malicious result.          |
| maliciousSubmitter  | uint32  | Identifier of the malicious submitter. |

### requireChallengeExtraGas

```solidity
function requireChallengeExtraGas(struct EcdsaDkg.Data self) internal view
```

Due to EIP150, 1/64 of the gas is not forwarded to the call, and will be kept to execute the remaining operations in the function after the call inside the try-catch.

To ensure there is no way for the caller to manipulate gas limit in such a way that the call inside try-catch fails with out-of-gas and the rest of the function is executed with the remaining 1/64 of gas, we require an extra gas amount to be left at the end of the call to the function challenging DKG result and wrapping the call to EcdsaDkgValidator and TokenStaking contracts inside a try-catch.

### isResultValid

```solidity
function isResultValid(struct EcdsaDkg.Data self, struct EcdsaDkg.Result result) internal view returns (bool, string)
```

Checks if DKG result is valid for the current DKG.

#### Parameters

| Name   | Type                   | Description |
| ------ | ---------------------- | ----------- |
| self   | struct EcdsaDkg.Data   |             |
| result | struct EcdsaDkg.Result | DKG result. |

#### Return Values

| Name | Type   | Description                                                                                  |
| ---- | ------ | -------------------------------------------------------------------------------------------- |
| \[0] | bool   | True if the result is valid. If the result is invalid it returns false and an error message. |
| \[1] | string |                                                                                              |

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
