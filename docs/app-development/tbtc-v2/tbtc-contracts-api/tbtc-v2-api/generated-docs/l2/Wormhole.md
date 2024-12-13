# Solidity API

## WormholeTypes

Namespace which groups all types relevant to Wormhole interfaces.

### VaaKey

```solidity
struct VaaKey {
  uint16 chainId;
  bytes32 emitterAddress;
  uint64 sequence;
}
```

## IWormhole

Wormhole interface.

See: https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/2b7db51f99b49eda99b44f4a044e751cb0b2e8ea/src/interfaces/IWormhole.sol#L6

### chainId

```solidity
function chainId() external view returns (uint16)
```

See: https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/2b7db51f99b49eda99b44f4a044e751cb0b2e8ea/src/interfaces/IWormhole.sol#L109

### messageFee

```solidity
function messageFee() external view returns (uint256)
```

See: https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/2b7db51f99b49eda99b44f4a044e751cb0b2e8ea/src/interfaces/IWormhole.sol#L117

## IWormholeRelayer

Wormhole Relayer interface.

See: https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/2b7db51f99b49eda99b44f4a044e751cb0b2e8ea/src/interfaces/IWormholeRelayer.sol#L74

### quoteEVMDeliveryPrice

```solidity
function quoteEVMDeliveryPrice(uint16 targetChain, uint256 receiverValue, uint256 gasLimit) external view returns (uint256 nativePriceQuote, uint256 targetChainRefundPerGasUnused)
```

See: https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/2b7db51f99b49eda99b44f4a044e751cb0b2e8ea/src/interfaces/IWormholeRelayer.sol#L442

### sendVaasToEvm

```solidity
function sendVaasToEvm(uint16 targetChain, address targetAddress, bytes payload, uint256 receiverValue, uint256 gasLimit, struct WormholeTypes.VaaKey[] vaaKeys, uint16 refundChain, address refundAddress) external payable returns (uint64 sequence)
```

See: https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/2b7db51f99b49eda99b44f4a044e751cb0b2e8ea/src/interfaces/IWormholeRelayer.sol#L182

## IWormholeReceiver

Wormhole Receiver interface.

See: https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/2b7db51f99b49eda99b44f4a044e751cb0b2e8ea/src/interfaces/IWormholeReceiver.sol#L8

### receiveWormholeMessages

```solidity
function receiveWormholeMessages(bytes payload, bytes[] additionalVaas, bytes32 sourceAddress, uint16 sourceChain, bytes32 deliveryHash) external payable
```

See: https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/2b7db51f99b49eda99b44f4a044e751cb0b2e8ea/src/interfaces/IWormholeReceiver.sol#L44

## IWormholeTokenBridge

Wormhole Token Bridge interface.

See: https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/2b7db51f99b49eda99b44f4a044e751cb0b2e8ea/src/interfaces/ITokenBridge.sol#L9

### completeTransferWithPayload

```solidity
function completeTransferWithPayload(bytes encodedVm) external returns (bytes)
```

### parseTransferWithPayload

```solidity
function parseTransferWithPayload(bytes encoded) external pure returns (struct IWormholeTokenBridge.TransferWithPayload transfer)
```

### transferTokens

```solidity
function transferTokens(address token, uint256 amount, uint16 recipientChain, bytes32 recipient, uint256 arbiterFee, uint32 nonce) external payable returns (uint64 sequence)
```

### transferTokensWithPayload

```solidity
function transferTokensWithPayload(address token, uint256 amount, uint16 recipientChain, bytes32 recipient, uint32 nonce, bytes payload) external payable returns (uint64 sequence)
```

### TransferWithPayload

```solidity
struct TransferWithPayload {
  uint8 payloadID;
  uint256 amount;
  bytes32 tokenAddress;
  uint16 tokenChain;
  bytes32 to;
  uint16 toChain;
  bytes32 fromAddress;
  bytes payload;
}
```

## WormholeUtils

Library for Wormhole utilities.

### toWormholeAddress

```solidity
function toWormholeAddress(address _address) internal pure returns (bytes32)
```

Converts Ethereum address into Wormhole format.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _address | address | The address to convert. |

### fromWormholeAddress

```solidity
function fromWormholeAddress(bytes32 _address) internal pure returns (address)
```

Converts Wormhole address into Ethereum format.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _address | bytes32 | The address to convert. |

### normalize

```solidity
function normalize(uint256 amount) internal pure returns (uint256)
```

Eliminates the dust that cannot be bridged with Wormhole
due to the decimal shift in the Wormhole Bridge contract.
See https://github.com/wormhole-foundation/wormhole/blob/96682bdbeb7c87bfa110eade0554b3d8cbf788d2/ethereum/contracts/bridge/Bridge.sol#L276-L288

