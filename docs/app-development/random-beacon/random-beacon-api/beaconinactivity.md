# BeaconInactivity

## BeaconInactivity

{% hint style="warning" %}
This file documents a contract which is not yet deployed to Mainnet.
{% endhint %}

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

The minimum number of group members needed to interact according to the protocol to produce a valid inactivity claim.

### signatureByteSize

```solidity
uint256 signatureByteSize
```

Size in bytes of a single signature produced by member supporting the inactivity claim.

### verifyClaim

```solidity
function verifyClaim(contract SortitionPool sortitionPool, struct BeaconInactivity.Claim claim, bytes groupPubKey, uint256 nonce, uint32[] groupMembers) external view returns (uint32[] inactiveMembers)
```

Verifies the inactivity claim according to the rules defined in `Claim` struct documentation. Reverts if verification fails.

Group members hash is validated upstream in RandomBeacon.notifyOperatorInactivity()

#### Parameters

| Name          | Type                          | Description                               |
| ------------- | ----------------------------- | ----------------------------------------- |
| sortitionPool | contract SortitionPool        | Sortition pool reference                  |
| claim         | struct BeaconInactivity.Claim | Inactivity claim                          |
| groupPubKey   | bytes                         | Public key of the group raising the claim |
| nonce         | uint256                       | Current nonce for group used in the claim |
| groupMembers  | uint32\[]                     | Identifiers of group members              |

#### Return Values

| Name            | Type      | Description                             |
| --------------- | --------- | --------------------------------------- |
| inactiveMembers | uint32\[] | Identifiers of members who are inactive |

### validateMembersIndices

```solidity
function validateMembersIndices(uint256[] indices, uint256 groupSize) internal pure
```

Validates members indices array. Array is considered valid if its size and each single index are in \[1, groupSize] range, indexes are unique, and sorted in an ascending order. Reverts if validation fails.

#### Parameters

| Name      | Type       | Description                  |
| --------- | ---------- | ---------------------------- |
| indices   | uint256\[] | Array to validate            |
| groupSize | uint256    | Group size used as reference |
