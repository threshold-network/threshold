# Solidity API

## EcdsaLib

### compressPublicKey

```solidity
function compressPublicKey(bytes32 x, bytes32 y) internal pure returns (bytes)
```

Converts public key X and Y coordinates (32-byte each) to a
compressed public key (33-byte). Compressed public key is X
coordinate prefixed with `02` or `03` based on the Y coordinate parity.
It is expected that the uncompressed public key is stripped
(i.e. it is not prefixed with `04`).

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| x | bytes32 | Wallet's public key's X coordinate. |
| y | bytes32 | Wallet's public key's Y coordinate. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bytes | Compressed public key (33-byte), prefixed with `02` or `03`. |

