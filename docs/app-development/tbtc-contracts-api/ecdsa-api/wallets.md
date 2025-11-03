# Wallets

## Wallets

### Wallet

```solidity
struct Wallet {
  bytes32 membersIdsHash;
  bytes32 publicKeyX;
  bytes32 publicKeyY;
}
```

### Data

```solidity
struct Data {
  mapping(bytes32 => struct Wallets.Wallet) registry;
  uint256[49] __gap;
}
```

### validatePublicKey

```solidity
function validatePublicKey(struct Wallets.Data self, bytes publicKey) internal view
```

Performs preliminary validation of a new group public key. The group public key must be unique and have 64 bytes in length. If the validation fails, the function reverts. This function must be called first for a public key of a wallet added with `addWallet` function.

#### Parameters

| Name      | Type                | Description                              |
| --------- | ------------------- | ---------------------------------------- |
| self      | struct Wallets.Data |                                          |
| publicKey | bytes               | Uncompressed public key of a new wallet. |

### addWallet

```solidity
function addWallet(struct Wallets.Data self, bytes32 membersIdsHash, bytes publicKey) internal returns (bytes32 walletID, bytes32 publicKeyX, bytes32 publicKeyY)
```

Registers a new wallet. This function does not validate parameters. The code calling this function must call `validatePublicKey` first.

Uses a public key hash as a unique identifier of a wallet.

#### Parameters

| Name           | Type                | Description                                       |
| -------------- | ------------------- | ------------------------------------------------- |
| self           | struct Wallets.Data |                                                   |
| membersIdsHash | bytes32             | Keccak256 hash of group members identifiers array |
| publicKey      | bytes               | Uncompressed public key                           |

#### Return Values

| Name       | Type    | Description                        |
| ---------- | ------- | ---------------------------------- |
| walletID   | bytes32 | Wallet's ID                        |
| publicKeyX | bytes32 | Wallet's public key's X coordinate |
| publicKeyY | bytes32 | Wallet's public key's Y coordinate |

### deleteWallet

```solidity
function deleteWallet(struct Wallets.Data self, bytes32 walletID) internal
```

Deletes wallet with the given ID from the registry. Reverts if wallet with the given ID has not been registered or if it has already been closed.

### isWalletRegistered

```solidity
function isWalletRegistered(struct Wallets.Data self, bytes32 walletID) internal view returns (bool)
```

Checks if a wallet with the given ID is registered.

#### Parameters

| Name     | Type                | Description |
| -------- | ------------------- | ----------- |
| self     | struct Wallets.Data |             |
| walletID | bytes32             | Wallet's ID |

#### Return Values

| Name | Type | Description                                     |
| ---- | ---- | ----------------------------------------------- |
| \[0] | bool | True if a wallet is registered, false otherwise |

### getWalletMembersIdsHash

```solidity
function getWalletMembersIdsHash(struct Wallets.Data self, bytes32 walletID) internal view returns (bytes32)
```

Returns Keccak256 hash of the wallet signing group members identifiers array. Group members do not include operators selected by the sortition pool that misbehaved during DKG. Reverts if wallet with the given ID is not registered.

#### Parameters

| Name     | Type                | Description      |
| -------- | ------------------- | ---------------- |
| self     | struct Wallets.Data |                  |
| walletID | bytes32             | ID of the wallet |

#### Return Values

| Name | Type    | Description                       |
| ---- | ------- | --------------------------------- |
| \[0] | bytes32 | Wallet signing group members hash |

### getWalletPublicKeyCoordinates

```solidity
function getWalletPublicKeyCoordinates(struct Wallets.Data self, bytes32 walletID) internal view returns (bytes32 x, bytes32 y)
```

Gets public key of a wallet with the given wallet ID. The public key is returned as X and Y coordinates. Reverts if wallet with the given ID is not registered.

#### Parameters

| Name     | Type                | Description      |
| -------- | ------------------- | ---------------- |
| self     | struct Wallets.Data |                  |
| walletID | bytes32             | ID of the wallet |

#### Return Values

| Name | Type    | Description             |
| ---- | ------- | ----------------------- |
| x    | bytes32 | Public key X coordinate |
| y    | bytes32 | Public key Y coordinate |

### getWalletPublicKey

```solidity
function getWalletPublicKey(struct Wallets.Data self, bytes32 walletID) internal view returns (bytes)
```

Gets public key of a wallet with the given wallet ID. The public key is returned in an uncompressed format as a 64-byte concatenation of X and Y coordinates. Reverts if wallet with the given ID is not registered.

#### Parameters

| Name     | Type                | Description      |
| -------- | ------------------- | ---------------- |
| self     | struct Wallets.Data |                  |
| walletID | bytes32             | ID of the wallet |

#### Return Values

| Name | Type  | Description                           |
| ---- | ----- | ------------------------------------- |
| \[0] | bytes | Uncompressed public key of the wallet |
