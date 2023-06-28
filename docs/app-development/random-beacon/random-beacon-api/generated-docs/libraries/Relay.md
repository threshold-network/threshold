# Solidity API

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

