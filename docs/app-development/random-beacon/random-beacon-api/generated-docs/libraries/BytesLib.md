# Solidity API

## BytesLib

### concatStorage

```solidity
function concatStorage(bytes _preBytes, bytes _postBytes) internal
```

### equalStorage

```solidity
function equalStorage(bytes _preBytes, bytes _postBytes) internal view returns (bool)
```

### concat

```solidity
function concat(bytes _preBytes, bytes _postBytes) internal pure returns (bytes)
```

### slice

```solidity
function slice(bytes _bytes, uint256 _start, uint256 _length) internal pure returns (bytes res)
```

### toAddress

```solidity
function toAddress(bytes _bytes, uint256 _start) internal pure returns (address)
```

### toUint8

```solidity
function toUint8(bytes _bytes, uint256 _start) internal pure returns (uint8)
```

### toUint

```solidity
function toUint(bytes _bytes, uint256 _start) internal pure returns (uint256)
```

### equal

```solidity
function equal(bytes _preBytes, bytes _postBytes) internal pure returns (bool)
```

### toBytes32

```solidity
function toBytes32(bytes _source) internal pure returns (bytes32 result)
```

### keccak256Slice

```solidity
function keccak256Slice(bytes _bytes, uint256 _start, uint256 _length) internal pure returns (bytes32 result)
```

