# LightRelay

## Epoch

```solidity
struct Epoch {
  uint32 timestamp;
  uint224 target;
}
```

## ILightRelay

### Genesis

```solidity
event Genesis(uint256 blockHeight)
```

### Retarget

```solidity
event Retarget(uint256 oldDifficulty, uint256 newDifficulty)
```

### ProofLengthChanged

```solidity
event ProofLengthChanged(uint256 newLength)
```

### AuthorizationRequirementChanged

```solidity
event AuthorizationRequirementChanged(bool newStatus)
```

### SubmitterAuthorized

```solidity
event SubmitterAuthorized(address submitter)
```

### SubmitterDeauthorized

```solidity
event SubmitterDeauthorized(address submitter)
```

### retarget

```solidity
function retarget(bytes headers) external
```

### validateChain

```solidity
function validateChain(bytes headers) external view returns (uint256 startingHeaderTimestamp, uint256 headerCount)
```

### getBlockDifficulty

```solidity
function getBlockDifficulty(uint256 blockNumber) external view returns (uint256)
```

### getEpochDifficulty

```solidity
function getEpochDifficulty(uint256 epochNumber) external view returns (uint256)
```

### getRelayRange

```solidity
function getRelayRange() external view returns (uint256 relayGenesis, uint256 currentEpochEnd)
```

## RelayUtils

### extractTimestampAt

```solidity
function extractTimestampAt(bytes headers, uint256 at) internal pure returns (uint32)
```

Extract the timestamp of the header at the given position.

Assumes that the specified position contains a valid header. Performs no validation whatsoever.

#### Parameters

| Name    | Type    | Description                                   |
| ------- | ------- | --------------------------------------------- |
| headers | bytes   | Byte array containing the header of interest. |
| at      | uint256 | The start of the header in the array.         |

#### Return Values

| Name | Type   | Description                  |
| ---- | ------ | ---------------------------- |
| \[0] | uint32 | The timestamp of the header. |

## LightRelay

THE RELAY MUST NOT BE USED BEFORE GENESIS AND AT LEAST ONE RETARGET.

### ready

```solidity
bool ready
```

### authorizationRequired

```solidity
bool authorizationRequired
```

### proofLength

```solidity
uint64 proofLength
```

### genesisEpoch

```solidity
uint64 genesisEpoch
```

### currentEpoch

```solidity
uint64 currentEpoch
```

### currentEpochDifficulty

```solidity
uint256 currentEpochDifficulty
```

### prevEpochDifficulty

```solidity
uint256 prevEpochDifficulty
```

### epochs

```solidity
mapping(uint256 => struct Epoch) epochs
```

### isAuthorized

```solidity
mapping(address => bool) isAuthorized
```

### relayActive

```solidity
modifier relayActive()
```

### genesis

```solidity
function genesis(bytes genesisHeader, uint256 genesisHeight, uint64 genesisProofLength) external
```

Establish a starting point for the relay by providing the target, timestamp and blockheight of the first block of the relay genesis epoch.

If the relay is used by querying the current and previous epoch difficulty, at least one retarget needs to be provided after genesis; otherwise the prevEpochDifficulty will be uninitialised and zero.

#### Parameters

| Name               | Type    | Description                                       |
| ------------------ | ------- | ------------------------------------------------- |
| genesisHeader      | bytes   | The first block header of the genesis epoch.      |
| genesisHeight      | uint256 | The block number of the first block of the epoch. |
| genesisProofLength | uint64  | The number of blocks required to accept a proof.  |

### setProofLength

```solidity
function setProofLength(uint64 newLength) external
```

Set the number of blocks required to accept a header chain.

For production, a high number (e.g. 20-50) is recommended. Small numbers are accepted but should only be used for testing.

#### Parameters

| Name      | Type   | Description                                            |
| --------- | ------ | ------------------------------------------------------ |
| newLength | uint64 | The required number of blocks. Must be less than 2016. |

### setAuthorizationStatus

```solidity
function setAuthorizationStatus(bool status) external
```

Set whether the relay requires retarget submitters to be pre-authorised by governance.

#### Parameters

| Name   | Type | Description                                            |
| ------ | ---- | ------------------------------------------------------ |
| status | bool | True if authorisation is to be required, false if not. |

### authorize

```solidity
function authorize(address submitter) external
```

Authorise the given address to submit retarget proofs.

#### Parameters

| Name      | Type    | Description                   |
| --------- | ------- | ----------------------------- |
| submitter | address | The address to be authorised. |

### deauthorize

```solidity
function deauthorize(address submitter) external
```

Rescind the authorisation of the submitter to retarget.

#### Parameters

| Name      | Type    | Description                     |
| --------- | ------- | ------------------------------- |
| submitter | address | The address to be deauthorised. |

### retarget

```solidity
function retarget(bytes headers) external
```

Add a new epoch to the relay by providing a proof of the difficulty before and after the retarget.

Checks that the first X blocks are valid in the most recent epoch, that the difficulty of the new epoch is calculated correctly according to the block timestamps, and that the next X blocks would be valid in the new epoch. We have no information of block heights, so we cannot enforce that retargets only happen every 2016 blocks; instead, we assume that this is the case if a valid proof of work is provided. It is possible to cheat the relay by providing X blocks from earlier in the most recent epoch, and then mining X new blocks after them. However, each of these malicious blocks would have to be mined to a higher difficulty than the legitimate ones. Alternatively, if the retarget has not been performed yet, one could first mine X blocks in the old difficulty with timestamps set far in the future, and then another X blocks at a greatly reduced difficulty. In either case, cheating the realy requires more work than mining X legitimate blocks. Only the most recent epoch is vulnerable to these attacks; once a retarget has been proven to the relay, the epoch is immutable even if a contradictory proof were to be presented later.

#### Parameters

| Name    | Type  | Description                                                                                                                                                     |
| ------- | ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| headers | bytes | A chain of headers including the last X blocks before the retarget, followed by the first X blocks after the retarget, where X equals the current proof length. |

### validateChain

```solidity
function validateChain(bytes headers) external view returns (uint256 startingHeaderTimestamp, uint256 headerCount)
```

Check whether a given chain of headers should be accepted as valid within the rules of the relay. If the validation fails, this function throws an exception.

A chain of headers is accepted as valid if:

* Its length is between 2 and 2015 headers.
* Headers in the chain are sequential and refer to previous digests.
* Each header is mined with the correct amount of work.
* The difficulty in each header matches an epoch of the relay, as determined by the headers' timestamps. The headers must be between the genesis epoch and the latest proven epoch (inclusive). If the chain contains a retarget, it is accepted if the retarget has already been proven to the relay. If the chain contains blocks of an epoch that has not been proven to the relay (after a retarget within the header chain, or when the entire chain falls within an epoch that has not been proven yet), it will be rejected. One exception to this is when two subsequent epochs have exactly the same difficulty; headers from the latter epoch will be accepted if the previous epoch has been proven to the relay. This is because it is not possible to distinguish such headers from headers of the previous epoch.

If the difficulty increases significantly between relay genesis and the present, creating fraudulent proofs for earlier epochs becomes easier. Users of the relay should check the timestamps of valid headers and only accept appropriately recent ones.

#### Parameters

| Name    | Type  | Description                           |
| ------- | ----- | ------------------------------------- |
| headers | bytes | A chain of 2 to 2015 bitcoin headers. |

#### Return Values

| Name                    | Type    | Description                        |
| ----------------------- | ------- | ---------------------------------- |
| startingHeaderTimestamp | uint256 | The timestamp of the first header. |
| headerCount             | uint256 | The number of headers.             |

### getBlockDifficulty

```solidity
function getBlockDifficulty(uint256 blockNumber) external view returns (uint256)
```

Get the difficulty of the specified block.

#### Parameters

| Name        | Type    | Description                                                                                                                                                       |
| ----------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| blockNumber | uint256 | The number of the block. Must fall within the relay range (at or after the relay genesis, and at or before the end of the most recent epoch proven to the relay). |

#### Return Values

| Name | Type    | Description                  |
| ---- | ------- | ---------------------------- |
| \[0] | uint256 | The difficulty of the epoch. |

### getRelayRange

```solidity
function getRelayRange() external view returns (uint256 relayGenesis, uint256 currentEpochEnd)
```

Get the range of blocks the relay can accept proofs for.

Assumes that the genesis has been set correctly. Additionally, if the next epoch after the current one has the exact same difficulty, headers for it can be validated as well. This function should be used for informative purposes, e.g. to determine whether a retarget must be provided before submitting a header chain for validation.

#### Return Values

| Name            | Type    | Description                                                                                       |
| --------------- | ------- | ------------------------------------------------------------------------------------------------- |
| relayGenesis    | uint256 | The height of the earliest block that can be included in header chains for the relay to validate. |
| currentEpochEnd | uint256 | The height of the last block that can be included in header chains for the relay to validate.     |

### getCurrentEpochDifficulty

```solidity
function getCurrentEpochDifficulty() external view virtual returns (uint256)
```

Returns the difficulty of the current epoch.

returns 0 if the relay is not ready.

#### Return Values

| Name | Type    | Description                          |
| ---- | ------- | ------------------------------------ |
| \[0] | uint256 | The difficulty of the current epoch. |

### getPrevEpochDifficulty

```solidity
function getPrevEpochDifficulty() external view virtual returns (uint256)
```

Returns the difficulty of the previous epoch.

Returns 0 if the relay is not ready or has not had a retarget.

#### Return Values

| Name | Type    | Description                           |
| ---- | ------- | ------------------------------------- |
| \[0] | uint256 | The difficulty of the previous epoch. |

### getCurrentAndPrevEpochDifficulty

```solidity
function getCurrentAndPrevEpochDifficulty() external view returns (uint256 current, uint256 previous)
```

### getEpochDifficulty

```solidity
function getEpochDifficulty(uint256 epochNumber) public view returns (uint256)
```

Get the difficulty of the specified epoch.

#### Parameters

| Name        | Type    | Description                                                                                                              |
| ----------- | ------- | ------------------------------------------------------------------------------------------------------------------------ |
| epochNumber | uint256 | The number of the epoch (the height of the first block of the epoch, divided by 2016). Must fall within the relay range. |

#### Return Values

| Name | Type    | Description                  |
| ---- | ------- | ---------------------------- |
| \[0] | uint256 | The difficulty of the epoch. |

### validateHeader

```solidity
function validateHeader(bytes headers, uint256 start, bytes32 prevDigest) internal view returns (bytes32 digest, uint256 target)
```

Check that the specified header forms a correct chain with the digest of the previous header (if provided), and has sufficient work.

Throws an exception if the header's chain or PoW are invalid. Performs no other validation.

#### Parameters

| Name       | Type    | Description                                                                                   |
| ---------- | ------- | --------------------------------------------------------------------------------------------- |
| headers    | bytes   | The byte array containing the header of interest.                                             |
| start      | uint256 | The start of the header in the array.                                                         |
| prevDigest | bytes32 | The digest of the previous header (optional; providing zeros for the digest skips the check). |

#### Return Values

| Name   | Type    | Description                       |
| ------ | ------- | --------------------------------- |
| digest | bytes32 | The digest of the current header. |
| target | uint256 | The PoW target of the header.     |
