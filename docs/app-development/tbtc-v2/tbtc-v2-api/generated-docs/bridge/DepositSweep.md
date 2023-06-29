# Solidity API

## DepositSweep

The library handles the logic for sweeping transactions revealed to
the Bridge

Bridge active wallet periodically signs a transaction that unlocks all
of the valid, revealed deposits above the dust threshold, combines them
into a single UTXO with the existing main wallet UTXO, and relocks
those transactions without a 30-day refund clause to the same wallet.
This has two main effects: it consolidates the UTXO set and it disables
the refund. Balances of depositors in the Bank are increased when the
SPV sweep proof is submitted to the Bridge.

### DepositSweepTxInputsProcessingInfo

```solidity
struct DepositSweepTxInputsProcessingInfo {
  bytes sweepTxInputVector;
  struct BitcoinTx.UTXO mainUtxo;
  address vault;
}
```

### DepositSweepTxInputsInfo

```solidity
struct DepositSweepTxInputsInfo {
  uint256 inputsTotalValue;
  address[] depositors;
  uint256[] depositedAmounts;
  uint256[] treasuryFees;
}
```

### DepositsSwept

```solidity
event DepositsSwept(bytes20 walletPubKeyHash, bytes32 sweepTxHash)
```

### submitDepositSweepProof

```solidity
function submitDepositSweepProof(struct BridgeState.Storage self, struct BitcoinTx.Info sweepTx, struct BitcoinTx.Proof sweepProof, struct BitcoinTx.UTXO mainUtxo, address vault) external
```

Used by the wallet to prove the BTC deposit sweep transaction
and to update Bank balances accordingly. Sweep is only accepted
if it satisfies SPV proof.

The function is performing Bank balance updates by first
computing the Bitcoin fee for the sweep transaction. The fee is
divided evenly between all swept deposits. Each depositor
receives a balance in the bank equal to the amount inferred
during the reveal transaction, minus their fee share.

It is possible to prove the given sweep only one time.

Requirements:
- `sweepTx` components must match the expected structure. See
`BitcoinTx.Info` docs for reference. Their values must exactly
correspond to appropriate Bitcoin transaction fields to produce
a provable transaction hash,
- The `sweepTx` should represent a Bitcoin transaction with 1..n
inputs. If the wallet has no main UTXO, all n inputs should
correspond to P2(W)SH revealed deposits UTXOs. If the wallet has
an existing main UTXO, one of the n inputs must point to that
main UTXO and remaining n-1 inputs should correspond to P2(W)SH
revealed deposits UTXOs. That transaction must have only
one P2(W)PKH output locking funds on the 20-byte wallet public
key hash,
- All revealed deposits that are swept by `sweepTx` must have
their `vault` parameters set to the same address as the address
passed in the `vault` function parameter,
- `sweepProof` components must match the expected structure. See
`BitcoinTx.Proof` docs for reference. The `bitcoinHeaders`
field must contain a valid number of block headers, not less
than the `txProofDifficultyFactor` contract constant,
- `mainUtxo` components must point to the recent main UTXO
of the given wallet, as currently known on the Ethereum chain.
If there is no main UTXO, this parameter is ignored.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| sweepTx | struct BitcoinTx.Info | Bitcoin sweep transaction data. |
| sweepProof | struct BitcoinTx.Proof | Bitcoin sweep proof data. |
| mainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. If no main UTXO exists for the given wallet, this parameter is ignored. |
| vault | address | Optional address of the vault where all swept deposits should be routed to. All deposits swept as part of the transaction must have their `vault` parameters set to the same address. If this parameter is set to an address of a trusted vault, swept deposits are routed to that vault. If this parameter is set to the zero address or to an address of a non-trusted vault, swept deposits are not routed to a vault but depositors' balances are increased in the Bank individually. |

### resolveDepositSweepingWallet

```solidity
function resolveDepositSweepingWallet(struct BridgeState.Storage self, bytes20 walletPubKeyHash, struct BitcoinTx.UTXO mainUtxo) internal view returns (struct Wallets.Wallet wallet, struct BitcoinTx.UTXO resolvedMainUtxo)
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

### processDepositSweepTxOutput

```solidity
function processDepositSweepTxOutput(struct BridgeState.Storage self, bytes sweepTxOutputVector) internal view returns (bytes20 walletPubKeyHash, uint64 value)
```

Processes the Bitcoin sweep transaction output vector by
extracting the single output and using it to gain additional
information required for further processing (e.g. value and
wallet public key hash).

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| sweepTxOutputVector | bytes | Bitcoin sweep transaction output vector. This function assumes vector's structure is valid so it must be validated using e.g. `BTCUtils.validateVout` function before it is passed here. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletPubKeyHash | bytes20 | 20-byte wallet public key hash. |
| value | uint64 | 8-byte sweep transaction output value. |

### processDepositSweepTxInputs

```solidity
function processDepositSweepTxInputs(struct BridgeState.Storage self, struct DepositSweep.DepositSweepTxInputsProcessingInfo processInfo) internal returns (struct DepositSweep.DepositSweepTxInputsInfo resultInfo)
```

Processes the Bitcoin sweep transaction input vector. It
extracts each input and tries to obtain associated deposit or
main UTXO data, depending on the input type. Reverts
if one of the inputs cannot be recognized as a pointer to a
revealed deposit or expected main UTXO.
This function also marks each processed deposit as swept.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| resultInfo | struct DepositSweep.DepositSweepTxInputsInfo | Outcomes of the processing. |

### parseDepositSweepTxInputAt

```solidity
function parseDepositSweepTxInputAt(bytes inputVector, uint256 inputStartingIndex) internal pure returns (bytes32 outpointTxHash, uint32 outpointIndex, uint256 inputLength)
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

### depositSweepTxFeeDistribution

```solidity
function depositSweepTxFeeDistribution(uint256 sweepTxInputsTotalValue, uint256 sweepTxOutputValue, uint256 depositsCount) internal pure returns (uint256 depositTxFee, uint256 depositTxFeeRemainder)
```

Determines the distribution of the sweep transaction fee
over swept deposits.

It is up to the caller to decide how the remainder should be
counted in. This function only computes its value.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| sweepTxInputsTotalValue | uint256 | Total value of all sweep transaction inputs. |
| sweepTxOutputValue | uint256 | Value of the sweep transaction output. |
| depositsCount | uint256 | Count of the deposits swept by the sweep transaction. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| depositTxFee | uint256 | Transaction fee per deposit determined by evenly spreading the divisible part of the sweep transaction fee over all deposits. |
| depositTxFeeRemainder | uint256 | The indivisible part of the sweep transaction fee than cannot be distributed over all deposits. |

