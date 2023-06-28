# Solidity API

## IWalletOwner

### __ecdsaWalletCreatedCallback

```solidity
function __ecdsaWalletCreatedCallback(bytes32 walletID, bytes32 publicKeyX, bytes32 publicKeyY) external
```

Callback function executed once a new wallet is created.

Should be callable only by the Wallet Registry.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletID | bytes32 | Wallet's unique identifier. |
| publicKeyX | bytes32 |  |
| publicKeyY | bytes32 | Wallet's public key's X coordinate. |

### __ecdsaWalletHeartbeatFailedCallback

```solidity
function __ecdsaWalletHeartbeatFailedCallback(bytes32 walletID, bytes32 publicKeyX, bytes32 publicKeyY) external
```

Callback function executed once a wallet heartbeat failure
is detected.

Should be callable only by the Wallet Registry.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletID | bytes32 | Wallet's unique identifier. |
| publicKeyX | bytes32 |  |
| publicKeyY | bytes32 | Wallet's public key's X coordinate. |

