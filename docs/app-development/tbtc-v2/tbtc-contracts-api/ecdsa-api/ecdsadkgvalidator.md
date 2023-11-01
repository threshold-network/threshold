# Solidity API

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

