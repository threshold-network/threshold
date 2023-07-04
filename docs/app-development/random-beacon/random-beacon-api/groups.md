# Solidity API

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

