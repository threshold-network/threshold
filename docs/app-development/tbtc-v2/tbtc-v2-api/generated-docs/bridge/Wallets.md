# Solidity API

## Wallets

Library responsible for handling integration between Bridge
contract and ECDSA wallets.

### WalletState

```solidity
enum WalletState {
  Unknown,
  Live,
  MovingFunds,
  Closing,
  Closed,
  Terminated
}
```

### Wallet

```solidity
struct Wallet {
  bytes32 ecdsaWalletID;
  bytes32 mainUtxoHash;
  uint64 pendingRedemptionsValue;
  uint32 createdAt;
  uint32 movingFundsRequestedAt;
  uint32 closingStartedAt;
  uint32 pendingMovedFundsSweepRequestsCount;
  enum Wallets.WalletState state;
  bytes32 movingFundsTargetWalletsCommitmentHash;
}
```

### NewWalletRequested

```solidity
event NewWalletRequested()
```

### NewWalletRegistered

```solidity
event NewWalletRegistered(bytes32 ecdsaWalletID, bytes20 walletPubKeyHash)
```

### WalletMovingFunds

```solidity
event WalletMovingFunds(bytes32 ecdsaWalletID, bytes20 walletPubKeyHash)
```

### WalletClosing

```solidity
event WalletClosing(bytes32 ecdsaWalletID, bytes20 walletPubKeyHash)
```

### WalletClosed

```solidity
event WalletClosed(bytes32 ecdsaWalletID, bytes20 walletPubKeyHash)
```

### WalletTerminated

```solidity
event WalletTerminated(bytes32 ecdsaWalletID, bytes20 walletPubKeyHash)
```

### requestNewWallet

```solidity
function requestNewWallet(struct BridgeState.Storage self, struct BitcoinTx.UTXO activeWalletMainUtxo) external
```

Requests creation of a new wallet. This function just
forms a request and the creation process is performed
asynchronously. Outcome of that process should be delivered
using `registerNewWallet` function.

Requirements:
- `activeWalletMainUtxo` components must point to the recent main
UTXO of the given active wallet, as currently known on the
Ethereum chain. If there is no active wallet at the moment, or
the active wallet has no main UTXO, this parameter can be
empty as it is ignored,
- Wallet creation must not be in progress,
- If the active wallet is set, one of the following
conditions must be true:
- The active wallet BTC balance is above the minimum threshold
and the active wallet is old enough, i.e. the creation period
was elapsed since its creation time,
- The active wallet BTC balance is above the maximum threshold.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| activeWalletMainUtxo | struct BitcoinTx.UTXO | Data of the active wallet's main UTXO, as currently known on the Ethereum chain. |

### registerNewWallet

```solidity
function registerNewWallet(struct BridgeState.Storage self, bytes32 ecdsaWalletID, bytes32 publicKeyX, bytes32 publicKeyY) external
```

Registers a new wallet. This function should be called
after the wallet creation process initiated using
`requestNewWallet` completes and brings the outcomes.

Requirements:
- The only caller authorized to call this function is `registry`,
- Given wallet data must not belong to an already registered wallet.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| ecdsaWalletID | bytes32 | Wallet's unique identifier. |
| publicKeyX | bytes32 | Wallet's public key's X coordinate. |
| publicKeyY | bytes32 | Wallet's public key's Y coordinate. |

### notifyWalletRedemptionTimeout

```solidity
function notifyWalletRedemptionTimeout(struct BridgeState.Storage self, bytes20 walletPubKeyHash, uint32[] walletMembersIDs) internal
```

Handles a notification about a wallet redemption timeout.
Triggers the wallet moving funds process only if the wallet is
still in the Live state. That means multiple action timeouts can
be reported for the same wallet but only the first report
requests the wallet to move their funds. Executes slashing if
the wallet is in Live or MovingFunds state. Allows to notify
redemption timeout also for a Terminated wallet in case the
redemption was requested before the wallet got terminated.

Requirements:
- The wallet must be in the `Live`, `MovingFunds`,
or `Terminated` state.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members. |

### notifyWalletHeartbeatFailed

```solidity
function notifyWalletHeartbeatFailed(struct BridgeState.Storage self, bytes32 publicKeyX, bytes32 publicKeyY) external
```

Handles a notification about a wallet heartbeat failure and
triggers the wallet moving funds process.

Requirements:
- The only caller authorized to call this function is `registry`,
- Wallet must be in Live state.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| publicKeyX | bytes32 | Wallet's public key's X coordinate. |
| publicKeyY | bytes32 | Wallet's public key's Y coordinate. |

### notifyWalletCloseable

```solidity
function notifyWalletCloseable(struct BridgeState.Storage self, bytes20 walletPubKeyHash, struct BitcoinTx.UTXO walletMainUtxo) external
```

Notifies that the wallet is either old enough or has too few
satoshis left and qualifies to be closed.

Requirements:
- Wallet must not be set as the current active wallet,
- Wallet must exceed the wallet maximum age OR the wallet BTC
balance must be lesser than the minimum threshold. If the latter
case is true, the `walletMainUtxo` components must point to the
recent main UTXO of the given wallet, as currently known on the
Ethereum chain. If the wallet has no main UTXO, this parameter
can be empty as it is ignored since the wallet balance is
assumed to be zero,
- Wallet must be in Live state.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |
| walletMainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. |

### notifyWalletClosingPeriodElapsed

```solidity
function notifyWalletClosingPeriodElapsed(struct BridgeState.Storage self, bytes20 walletPubKeyHash) internal
```

Notifies about the end of the closing period for the given wallet.
Closes the wallet ultimately and notifies the ECDSA registry
about this fact.

Requirements:
- The wallet must be in the Closing state,
- The wallet closing period must have elapsed.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |

### notifyWalletFundsMoved

```solidity
function notifyWalletFundsMoved(struct BridgeState.Storage self, bytes20 walletPubKeyHash, bytes32 targetWalletsHash) internal
```

Notifies that the wallet completed the moving funds process
successfully. Checks if the funds were moved to the expected
target wallets. Closes the source wallet if everything went
good and reverts otherwise.

Requirements:
- The caller must make sure the moving funds transaction actually
happened on Bitcoin chain and fits the protocol requirements,
- The source wallet must be in the MovingFunds state,
- The target wallets commitment must be submitted by the source
wallet,
- The actual target wallets used in the moving funds transaction
must be exactly the same as the target wallets commitment.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |
| targetWalletsHash | bytes32 | 32-byte keccak256 hash over the list of 20-byte public key hashes of the target wallets actually used within the moving funds transactions. |

### notifyWalletMovingFundsBelowDust

```solidity
function notifyWalletMovingFundsBelowDust(struct BridgeState.Storage self, bytes20 walletPubKeyHash) internal
```

Called when a MovingFunds wallet has a balance below the dust
threshold. Begins the wallet closing.

Requirements:
- The wallet must be in the MovingFunds state.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |

### notifyWalletMovingFundsTimeout

```solidity
function notifyWalletMovingFundsTimeout(struct BridgeState.Storage self, bytes20 walletPubKeyHash, uint32[] walletMembersIDs) internal
```

Called when the timeout for MovingFunds for the wallet elapsed.
Slashes wallet members and terminates the wallet.

Requirements:
- The wallet must be in the MovingFunds state.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members. |

### notifyWalletMovedFundsSweepTimeout

```solidity
function notifyWalletMovedFundsSweepTimeout(struct BridgeState.Storage self, bytes20 walletPubKeyHash, uint32[] walletMembersIDs) internal
```

Called when a wallet which was asked to sweep funds moved from
another wallet did not provide a sweeping proof before a timeout.
Slashes and terminates the wallet who failed to provide a proof.

Requirements:
- The wallet must be in the `Live`, `MovingFunds`,
or `Terminated` state.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet which was supposed to sweep funds. |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members. |

### notifyWalletFraudChallengeDefeatTimeout

```solidity
function notifyWalletFraudChallengeDefeatTimeout(struct BridgeState.Storage self, bytes20 walletPubKeyHash, uint32[] walletMembersIDs, address challenger) internal
```

Called when a wallet which was challenged for a fraud did not
defeat the challenge before the timeout. Slashes and terminates
the wallet who failed to defeat the challenge. If the wallet is
already terminated, it does nothing.

Requirements:
- The wallet must be in the `Live`, `MovingFunds`, `Closing`
or `Terminated` state.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet which was supposed to sweep funds. |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members. |
| challenger | address | Address of the party which submitted the fraud challenge. |

### moveFunds

```solidity
function moveFunds(struct BridgeState.Storage self, bytes20 walletPubKeyHash) internal
```

Requests a wallet to move their funds. If the wallet balance
is zero, the wallet closing begins immediately. If the move
funds request refers to the current active wallet, such a wallet
is no longer considered active and the active wallet slot
is unset allowing to trigger a new wallet creation immediately.

Requirements:
- The caller must make sure that the wallet is in the Live state.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |

### beginWalletClosing

```solidity
function beginWalletClosing(struct BridgeState.Storage self, bytes20 walletPubKeyHash) internal
```

Begins the closing period of the given wallet.

Requirements:
- The caller must make sure that the wallet is in the
MovingFunds state.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |

### finalizeWalletClosing

```solidity
function finalizeWalletClosing(struct BridgeState.Storage self, bytes20 walletPubKeyHash) internal
```

Finalizes the closing period of the given wallet and notifies
the ECDSA registry about this fact.

Requirements:
- The caller must make sure that the wallet is in the Closing state.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |

### terminateWallet

```solidity
function terminateWallet(struct BridgeState.Storage self, bytes20 walletPubKeyHash) internal
```

Terminates the given wallet and notifies the ECDSA registry
about this fact. If the wallet termination refers to the current
active wallet, such a wallet is no longer considered active and
the active wallet slot is unset allowing to trigger a new wallet
creation immediately.

Requirements:
- The caller must make sure that the wallet is in the
Live or MovingFunds or Closing state.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |

### getWalletBtcBalance

```solidity
function getWalletBtcBalance(struct BridgeState.Storage self, bytes20 walletPubKeyHash, struct BitcoinTx.UTXO walletMainUtxo) internal view returns (uint64 walletBtcBalance)
```

Gets BTC balance for given the wallet.

Requirements:
- `walletMainUtxo` components must point to the recent main UTXO
of the given wallet, as currently known on the Ethereum chain.
If the wallet has no main UTXO, this parameter can be empty as it
is ignored.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |
| walletMainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletBtcBalance | uint64 | Current BTC balance for the given wallet. |

