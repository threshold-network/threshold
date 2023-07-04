# Solidity API

## MovingFunds

The library handles the logic for moving Bitcoin between Bridge
wallets.

A wallet that failed a heartbeat, did not process requested redemption
on time, or qualifies to be closed, begins the procedure of moving
funds to other wallets in the Bridge. The wallet needs to commit to
which other Live wallets it is moving the funds to and then, provide an
SPV proof of moving funds to the previously committed wallets.
Once the proof is submitted, all target wallets are supposed to
sweep the received UTXOs with their own main UTXOs in order to
update their BTC balances.

### MovingFundsTxOutputsProcessingInfo

```solidity
struct MovingFundsTxOutputsProcessingInfo {
  bytes32 movingFundsTxHash;
  bytes movingFundsTxOutputVector;
}
```

### MovedFundsSweepRequestState

```solidity
enum MovedFundsSweepRequestState {
  Unknown,
  Pending,
  Processed,
  TimedOut
}
```

### MovedFundsSweepRequest

```solidity
struct MovedFundsSweepRequest {
  bytes20 walletPubKeyHash;
  uint64 value;
  uint32 createdAt;
  enum MovingFunds.MovedFundsSweepRequestState state;
}
```

### MovingFundsCommitmentSubmitted

```solidity
event MovingFundsCommitmentSubmitted(bytes20 walletPubKeyHash, bytes20[] targetWallets, address submitter)
```

### MovingFundsTimeoutReset

```solidity
event MovingFundsTimeoutReset(bytes20 walletPubKeyHash)
```

### MovingFundsCompleted

```solidity
event MovingFundsCompleted(bytes20 walletPubKeyHash, bytes32 movingFundsTxHash)
```

### MovingFundsTimedOut

```solidity
event MovingFundsTimedOut(bytes20 walletPubKeyHash)
```

### MovingFundsBelowDustReported

```solidity
event MovingFundsBelowDustReported(bytes20 walletPubKeyHash)
```

### MovedFundsSwept

```solidity
event MovedFundsSwept(bytes20 walletPubKeyHash, bytes32 sweepTxHash)
```

### MovedFundsSweepTimedOut

```solidity
event MovedFundsSweepTimedOut(bytes20 walletPubKeyHash, bytes32 movingFundsTxHash, uint32 movingFundsTxOutputIndex)
```

### submitMovingFundsCommitment

```solidity
function submitMovingFundsCommitment(struct BridgeState.Storage self, bytes20 walletPubKeyHash, struct BitcoinTx.UTXO walletMainUtxo, uint32[] walletMembersIDs, uint256 walletMemberIndex, bytes20[] targetWallets) external
```

Submits the moving funds target wallets commitment.
Once all requirements are met, that function registers the
target wallets commitment and opens the way for moving funds
proof submission.

Requirements:
- The source wallet must be in the MovingFunds state,
- The source wallet must not have pending redemption requests,
- The source wallet must not have pending moved funds sweep requests,
- The source wallet must not have submitted its commitment already,
- The expression `keccak256(abi.encode(walletMembersIDs))` must
be exactly the same as the hash stored under `membersIdsHash`
for the given source wallet in the ECDSA registry. Those IDs are
not directly stored in the contract for gas efficiency purposes
but they can be read from appropriate `DkgResultSubmitted`
and `DkgResultApproved` events,
- The `walletMemberIndex` must be in range [1, walletMembersIDs.length],
- The caller must be the member of the source wallet signing group
at the position indicated by `walletMemberIndex` parameter,
- The `walletMainUtxo` components must point to the recent main
UTXO of the source wallet, as currently known on the Ethereum
chain,
- Source wallet BTC balance must be greater than zero,
- At least one Live wallet must exist in the system,
- Submitted target wallets count must match the expected count
`N = min(liveWalletsCount, ceil(walletBtcBalance / walletMaxBtcTransfer))`
where `N > 0`,
- Each target wallet must be not equal to the source wallet,
- Each target wallet must follow the expected order i.e. all
target wallets 20-byte public key hashes represented as numbers
must form a strictly increasing sequence without duplicates,
- Each target wallet must be in Live state.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the source wallet. |
| walletMainUtxo | struct BitcoinTx.UTXO | Data of the source wallet's main UTXO, as currently known on the Ethereum chain. |
| walletMembersIDs | uint32[] | Identifiers of the source wallet signing group members. |
| walletMemberIndex | uint256 | Position of the caller in the source wallet signing group members list. |
| targetWallets | bytes20[] | List of 20-byte public key hashes of the target wallets that the source wallet commits to move the funds to. |

### resetMovingFundsTimeout

```solidity
function resetMovingFundsTimeout(struct BridgeState.Storage self, bytes20 walletPubKeyHash) external
```

Resets the moving funds timeout for the given wallet if the
target wallet commitment cannot be submitted due to a lack
of live wallets in the system.

Requirements:
- The wallet must be in the MovingFunds state,
- The target wallets commitment must not be already submitted for
the given moving funds wallet,
- Live wallets count must be zero,
- The moving funds timeout reset delay must be elapsed.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the moving funds wallet |

### submitMovingFundsProof

```solidity
function submitMovingFundsProof(struct BridgeState.Storage self, struct BitcoinTx.Info movingFundsTx, struct BitcoinTx.Proof movingFundsProof, struct BitcoinTx.UTXO mainUtxo, bytes20 walletPubKeyHash) external
```

Used by the wallet to prove the BTC moving funds transaction
and to make the necessary state changes. Moving funds is only
accepted if it satisfies SPV proof.

The function validates the moving funds transaction structure
by checking if it actually spends the main UTXO of the declared
wallet and locks the value on the pre-committed target wallets
using a reasonable transaction fee. If all preconditions are
met, this functions closes the source wallet.

It is possible to prove the given moving funds transaction only
one time.

Requirements:
- `movingFundsTx` components must match the expected structure. See
`BitcoinTx.Info` docs for reference. Their values must exactly
correspond to appropriate Bitcoin transaction fields to produce
a provable transaction hash,
- The `movingFundsTx` should represent a Bitcoin transaction with
exactly 1 input that refers to the wallet's main UTXO. That
transaction should have 1..n outputs corresponding to the
pre-committed target wallets. Outputs must be ordered in the
same way as their corresponding target wallets are ordered
within the target wallets commitment,
- `movingFundsProof` components must match the expected structure.
See `BitcoinTx.Proof` docs for reference. The `bitcoinHeaders`
field must contain a valid number of block headers, not less
than the `txProofDifficultyFactor` contract constant,
- `mainUtxo` components must point to the recent main UTXO
of the given wallet, as currently known on the Ethereum chain.
Additionally, the recent main UTXO on Ethereum must be set,
- `walletPubKeyHash` must be connected with the main UTXO used
as transaction single input,
- The wallet that `walletPubKeyHash` points to must be in the
MovingFunds state,
- The target wallets commitment must be submitted by the wallet
that `walletPubKeyHash` points to,
- The total Bitcoin transaction fee must be lesser or equal
to `movingFundsTxMaxTotalFee` governable parameter.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| movingFundsTx | struct BitcoinTx.Info | Bitcoin moving funds transaction data. |
| movingFundsProof | struct BitcoinTx.Proof | Bitcoin moving funds proof data. |
| mainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. |
| walletPubKeyHash | bytes20 | 20-byte public key hash (computed using Bitcoin HASH160 over the compressed ECDSA public key) of the wallet which performed the moving funds transaction. |

### processMovingFundsTxOutputs

```solidity
function processMovingFundsTxOutputs(struct BridgeState.Storage self, struct MovingFunds.MovingFundsTxOutputsProcessingInfo processInfo) internal returns (bytes32 targetWalletsHash, uint256 outputsTotalValue)
```

Processes the moving funds Bitcoin transaction output vector
and extracts information required for further processing.

Requirements:
- The `movingFundsTxOutputVector` must be parseable, i.e. must
be validated by the caller as stated in their parameter doc,
- Each output must refer to a 20-byte public key hash,
- The total outputs value must be evenly divided over all outputs.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| processInfo | struct MovingFunds.MovingFundsTxOutputsProcessingInfo | Processing info containing the moving funds tx hash and output vector. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| targetWalletsHash | bytes32 | keccak256 hash over the list of actual target wallets used in the transaction. |
| outputsTotalValue | uint256 | Sum of all outputs values. |

### notifyMovingFundsTimeout

```solidity
function notifyMovingFundsTimeout(struct BridgeState.Storage self, bytes20 walletPubKeyHash, uint32[] walletMembersIDs) external
```

Notifies about a timed out moving funds process. Terminates
the wallet and slashes signing group members as a result.

Requirements:
- The wallet must be in the MovingFunds state,
- The moving funds timeout must be actually exceeded,
- The expression `keccak256(abi.encode(walletMembersIDs))` must
be exactly the same as the hash stored under `membersIdsHash`
for the given `walletID`. Those IDs are not directly stored
in the contract for gas efficiency purposes but they can be
read from appropriate `DkgResultSubmitted` and `DkgResultApproved`
events of the `WalletRegistry` contract.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members. |

### notifyMovingFundsBelowDust

```solidity
function notifyMovingFundsBelowDust(struct BridgeState.Storage self, bytes20 walletPubKeyHash, struct BitcoinTx.UTXO mainUtxo) external
```

Notifies about a moving funds wallet whose BTC balance is
below the moving funds dust threshold. Ends the moving funds
process and begins wallet closing immediately.

Requirements:
- The wallet must be in the MovingFunds state,
- The `mainUtxo` components must point to the recent main UTXO
of the given wallet, as currently known on the Ethereum chain.
If the wallet has no main UTXO, this parameter can be empty as it
is ignored,
- The wallet BTC balance must be below the moving funds threshold.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |
| mainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. |

### submitMovedFundsSweepProof

```solidity
function submitMovedFundsSweepProof(struct BridgeState.Storage self, struct BitcoinTx.Info sweepTx, struct BitcoinTx.Proof sweepProof, struct BitcoinTx.UTXO mainUtxo) external
```

Used by the wallet to prove the BTC moved funds sweep
transaction and to make the necessary state changes. Moved
funds sweep is only accepted if it satisfies SPV proof.

The function validates the sweep transaction structure by
checking if it actually spends the moved funds UTXO and the
sweeping wallet's main UTXO (optionally), and if it locks the
value on the sweeping wallet's 20-byte public key hash using a
reasonable transaction fee. If all preconditions are
met, this function updates the sweeping wallet main UTXO, thus
their BTC balance.

It is possible to prove the given sweep transaction only
one time.

Requirements:
- `sweepTx` components must match the expected structure. See
`BitcoinTx.Info` docs for reference. Their values must exactly
correspond to appropriate Bitcoin transaction fields to produce
a provable transaction hash,
- The `sweepTx` should represent a Bitcoin transaction with
the first input pointing to a wallet's sweep Pending request and,
optionally, the second input pointing to the wallet's main UTXO,
if the sweeping wallet has a main UTXO set. There should be only
one output locking funds on the sweeping wallet 20-byte public
key hash,
- `sweepProof` components must match the expected structure.
See `BitcoinTx.Proof` docs for reference. The `bitcoinHeaders`
field must contain a valid number of block headers, not less
than the `txProofDifficultyFactor` contract constant,
- `mainUtxo` components must point to the recent main UTXO
of the sweeping wallet, as currently known on the Ethereum chain.
If there is no main UTXO, this parameter is ignored,
- The sweeping wallet must be in the Live or MovingFunds state,
- The total Bitcoin transaction fee must be lesser or equal
to `movedFundsSweepTxMaxTotalFee` governable parameter.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| sweepTx | struct BitcoinTx.Info | Bitcoin sweep funds transaction data. |
| sweepProof | struct BitcoinTx.Proof | Bitcoin sweep funds proof data. |
| mainUtxo | struct BitcoinTx.UTXO | Data of the sweeping wallet's main UTXO, as currently known on the Ethereum chain. |

### processMovedFundsSweepTxOutput

```solidity
function processMovedFundsSweepTxOutput(struct BridgeState.Storage self, bytes sweepTxOutputVector) internal view returns (bytes20 walletPubKeyHash, uint64 value)
```

Processes the Bitcoin moved funds sweep transaction output vector
by extracting the single output and using it to gain additional
information required for further processing (e.g. value and
wallet public key hash).

Requirements:
- Output vector must contain only one output,
- The single output must be of P2PKH or P2WPKH type and lock the
funds on a 20-byte public key hash.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| sweepTxOutputVector | bytes | Bitcoin moved funds sweep transaction output vector. This function assumes vector's structure is valid so it must be validated using e.g. `BTCUtils.validateVout` function before it is passed here. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletPubKeyHash | bytes20 | 20-byte wallet public key hash. |
| value | uint64 | 8-byte moved funds sweep transaction output value. |

### resolveMovedFundsSweepingWallet

```solidity
function resolveMovedFundsSweepingWallet(struct BridgeState.Storage self, bytes20 walletPubKeyHash, struct BitcoinTx.UTXO mainUtxo) internal view returns (struct Wallets.Wallet wallet, struct BitcoinTx.UTXO resolvedMainUtxo)
```

Resolves sweeping wallet based on the provided wallet public key
hash. Validates the wallet state and current main UTXO, as
currently known on the Ethereum chain.

Requirements:
- Sweeping wallet must be either in Live or MovingFunds state,
- If the main UTXO of the sweeping wallet exists in the storage,
the passed `mainUTXO` parameter must be equal to the stored one.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | public key hash of the wallet proving the sweep Bitcoin transaction. |
| mainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. If no main UTXO exists for the given wallet, this parameter is ignored. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| wallet | struct Wallets.Wallet | Data of the sweeping wallet. |
| resolvedMainUtxo | struct BitcoinTx.UTXO | The actual main UTXO of the sweeping wallet resolved by cross-checking the `mainUtxo` parameter with the chain state. If the validation went well, this is the plain-text main UTXO corresponding to the `wallet.mainUtxoHash`. |

### processMovedFundsSweepTxInputs

```solidity
function processMovedFundsSweepTxInputs(struct BridgeState.Storage self, bytes sweepTxInputVector, struct BitcoinTx.UTXO mainUtxo, bytes20 walletPubKeyHash) internal returns (uint256 inputsTotalValue)
```

Processes the Bitcoin moved funds sweep transaction input vector.
It extracts the first input and tries to match it with one of
the moved funds sweep requests targeting the sweeping wallet.
If the sweep request is an existing Pending request, this
function marks it as Processed. If the sweeping wallet has a
main UTXO, this function extracts the second input, makes sure
it refers to the wallet main UTXO, and marks that main UTXO as
correctly spent.

Requirements:
- The input vector must consist of one mandatory and one optional
input,
- The mandatory input must be the first input in the vector,
- The mandatory input must point to a Pending moved funds sweep
request that is targeted to the sweeping wallet,
- The optional output must be the second input in the vector,
- The optional input is required if the sweeping wallet has a
main UTXO (i.e. the `mainUtxo` is not zeroed). In that case,
that input must point the the sweeping wallet main UTXO.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| sweepTxInputVector | bytes | Bitcoin moved funds sweep transaction input vector. This function assumes vector's structure is valid so it must be validated using e.g. `BTCUtils.validateVin` function before it is passed here. |
| mainUtxo | struct BitcoinTx.UTXO | Data of the sweeping wallet's main UTXO. If no main UTXO exists for the given the wallet, this parameter's fields should be zeroed to bypass the main UTXO validation. |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the sweeping wallet. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| inputsTotalValue | uint256 | Total inputs value sum. |

### parseMovedFundsSweepTxInputAt

```solidity
function parseMovedFundsSweepTxInputAt(bytes inputVector, uint256 inputStartingIndex) internal pure returns (bytes32 outpointTxHash, uint32 outpointIndex, uint256 inputLength)
```

Parses a Bitcoin transaction input starting at the given index.

This function assumes vector's structure is valid so it must be
validated using e.g. `BTCUtils.validateVin` function before it
is passed here.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| inputVector | bytes | Bitcoin transaction input vector. |
| inputStartingIndex | uint256 | Index the given input starts at. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| outpointTxHash | bytes32 | 32-byte hash of the Bitcoin transaction which is pointed in the given input's outpoint. |
| outpointIndex | uint32 | 4-byte index of the Bitcoin transaction output which is pointed in the given input's outpoint. |
| inputLength | uint256 | Byte length of the given input. |

### notifyMovedFundsSweepTimeout

```solidity
function notifyMovedFundsSweepTimeout(struct BridgeState.Storage self, bytes32 movingFundsTxHash, uint32 movingFundsTxOutputIndex, uint32[] walletMembersIDs) external
```

Notifies about a timed out moved funds sweep process. If the
wallet is not terminated yet, that function terminates
the wallet and slashes signing group members as a result.
Marks the given sweep request as TimedOut.

Requirements:
- The moved funds sweep request must be in the Pending state,
- The moved funds sweep timeout must be actually exceeded,
- The wallet must be either in the Live or MovingFunds or
Terminated state,,
- The expression `keccak256(abi.encode(walletMembersIDs))` must
be exactly the same as the hash stored under `membersIdsHash`
for the given `walletID`. Those IDs are not directly stored
in the contract for gas efficiency purposes but they can be
read from appropriate `DkgResultSubmitted` and `DkgResultApproved`
events of the `WalletRegistry` contract.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| movingFundsTxHash | bytes32 | 32-byte hash of the moving funds transaction that caused the sweep request to be created. |
| movingFundsTxOutputIndex | uint32 | Index of the moving funds transaction output that is subject of the sweep request. |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members. |

