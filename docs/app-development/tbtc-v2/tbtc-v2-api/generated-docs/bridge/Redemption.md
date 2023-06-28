# Solidity API

## OutboundTx

Aggregates functions common to the redemption transaction proof
validation and to the moving funds transaction proof validation.

### processWalletOutboundTxInput

```solidity
function processWalletOutboundTxInput(struct BridgeState.Storage self, bytes walletOutboundTxInputVector, struct BitcoinTx.UTXO mainUtxo) internal
```

Checks whether an outbound Bitcoin transaction performed from
the given wallet has an input vector that contains a single
input referring to the wallet's main UTXO. Marks that main UTXO
as correctly spent if the validation succeeds. Reverts otherwise.
There are two outbound transactions from a wallet possible: a
redemption transaction or a moving funds to another wallet
transaction.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletOutboundTxInputVector | bytes | Bitcoin outbound transaction's input vector. This function assumes vector's structure is valid so it must be validated using e.g. `BTCUtils.validateVin` function before it is passed here. |
| mainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. |

### parseWalletOutboundTxInput

```solidity
function parseWalletOutboundTxInput(bytes walletOutboundTxInputVector) internal pure returns (bytes32 outpointTxHash, uint32 outpointIndex)
```

Parses the input vector of an outbound Bitcoin transaction
performed from the given wallet. It extracts the single input
then the transaction hash and output index from its outpoint.
There are two outbound transactions from a wallet possible: a
redemption transaction or a moving funds to another wallet
transaction.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletOutboundTxInputVector | bytes | Bitcoin outbound transaction input vector. This function assumes vector's structure is valid so it must be validated using e.g. `BTCUtils.validateVin` function before it is passed here. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| outpointTxHash | bytes32 | 32-byte hash of the Bitcoin transaction which is pointed in the input's outpoint. |
| outpointIndex | uint32 | 4-byte index of the Bitcoin transaction output which is pointed in the input's outpoint. |

## Redemption

The library handles the logic for redeeming Bitcoin balances from
the Bridge.

To initiate a redemption, a user with a Bank balance supplies
a Bitcoin address. Then, the system calculates the redemption fee, and
releases balance to the provided Bitcoin address. Just like in case of
sweeps of revealed deposits, redemption requests are processed in
batches and require SPV proof to be submitted to the Bridge.

### RedemptionRequest

```solidity
struct RedemptionRequest {
  address redeemer;
  uint64 requestedAmount;
  uint64 treasuryFee;
  uint64 txMaxFee;
  uint32 requestedAt;
}
```

### RedemptionTxOutputsInfo

```solidity
struct RedemptionTxOutputsInfo {
  uint256 outputsTotalValue;
  uint64 totalBurnableValue;
  uint64 totalTreasuryFee;
  uint32 changeIndex;
  uint64 changeValue;
}
```

### RedemptionTxOutputsProcessingInfo

```solidity
struct RedemptionTxOutputsProcessingInfo {
  uint256 outputStartingIndex;
  uint256 outputsCount;
  bytes32 walletP2PKHScriptKeccak;
  bytes32 walletP2WPKHScriptKeccak;
}
```

### RedemptionRequested

```solidity
event RedemptionRequested(bytes20 walletPubKeyHash, bytes redeemerOutputScript, address redeemer, uint64 requestedAmount, uint64 treasuryFee, uint64 txMaxFee)
```

### RedemptionsCompleted

```solidity
event RedemptionsCompleted(bytes20 walletPubKeyHash, bytes32 redemptionTxHash)
```

### RedemptionTimedOut

```solidity
event RedemptionTimedOut(bytes20 walletPubKeyHash, bytes redeemerOutputScript)
```

### requestRedemption

```solidity
function requestRedemption(struct BridgeState.Storage self, bytes20 walletPubKeyHash, struct BitcoinTx.UTXO mainUtxo, address balanceOwner, bytes redeemerOutputScript, uint64 amount) external
```

Requests redemption of the given amount from the specified
wallet to the redeemer Bitcoin output script.
This function handles the simplest case, where balance owner is
the redeemer.

Requirements:
- Wallet behind `walletPubKeyHash` must be live,
- `mainUtxo` components must point to the recent main UTXO
of the given wallet, as currently known on the Ethereum chain,
- `redeemerOutputScript` must be a proper Bitcoin script,
- `redeemerOutputScript` cannot have wallet PKH as payload,
- `amount` must be above or equal the `redemptionDustThreshold`,
- Given `walletPubKeyHash` and `redeemerOutputScript` pair can be
used for only one pending request at the same time,
- Wallet must have enough Bitcoin balance to proceed the request,
- Balance owner must make an allowance in the Bank that the Bridge
contract can spend the given `amount`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | The 20-byte wallet public key hash (computed using Bitcoin HASH160 over the compressed ECDSA public key). |
| mainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. |
| balanceOwner | address | The address of the Bank balance owner whose balance is getting redeemed. Balance owner address is stored as a redeemer address who will be able co claim back the Bank balance if anything goes wrong during the redemption. |
| redeemerOutputScript | bytes | The redeemer's length-prefixed output script (P2PKH, P2WPKH, P2SH or P2WSH) that will be used to lock redeemed BTC. |
| amount | uint64 | Requested amount in satoshi. This is also the Bank balance that is taken from the `balanceOwner` upon request. Once the request is handled, the actual amount of BTC locked on the redeemer output script will be always lower than this value since the treasury and Bitcoin transaction fees must be incurred. The minimal amount satisfying the request can be computed as: `amount - (amount / redemptionTreasuryFeeDivisor) - redemptionTxMaxFee`. Fees values are taken at the moment of request creation. |

### requestRedemption

```solidity
function requestRedemption(struct BridgeState.Storage self, address balanceOwner, uint64 amount, bytes redemptionData) external
```

Requests redemption of the given amount from the specified
wallet to the redeemer Bitcoin output script. Used by
`Bridge.receiveBalanceApproval`. Can handle more complex cases
where balance owner may be someone else than the redeemer.

Requirements:
- Wallet behind `walletPubKeyHash` must be live,
- `mainUtxo*` components must point to the recent main UTXO
of the given wallet, as currently known on the Ethereum chain,
- `redeemerOutputScript` must be a proper Bitcoin script,
- `redeemerOutputScript` cannot have wallet PKH as payload,
- `amount` must be above or equal the `redemptionDustThreshold`,
- Given `walletPubKeyHash` and `redeemerOutputScript` pair can be
used for only one pending request at the same time,
- Wallet must have enough Bitcoin balance to proceed the request,
- Balance owner must make an allowance in the Bank that the Bridge
contract can spend the given `amount`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| balanceOwner | address | The address of the Bank balance owner whose balance is getting redeemed. |
| amount | uint64 | Requested amount in satoshi. This is also the Bank balance that is taken from the `balanceOwner` upon request. Once the request is handled, the actual amount of BTC locked on the redeemer output script will be always lower than this value since the treasury and Bitcoin transaction fees must be incurred. The minimal amount satisfying the request can be computed as: `amount - (amount / redemptionTreasuryFeeDivisor) - redemptionTxMaxFee`. Fees values are taken at the moment of request creation. |
| redemptionData | bytes | ABI-encoded redemption data: [ address redeemer, bytes20 walletPubKeyHash, bytes32 mainUtxoTxHash, uint32 mainUtxoTxOutputIndex, uint64 mainUtxoTxOutputValue, bytes redeemerOutputScript ] - redeemer: The Ethereum address of the redeemer who will be able to claim Bank balance if anything goes wrong during the redemption. In the most basic case, when someone redeems their Bitcoin balance from the Bank, `balanceOwner` is the same as `redeemer`. However, when a Vault is redeeming part of its balance for some redeemer address (for example, someone who has earlier deposited into that Vault), `balanceOwner` is the Vault, and `redeemer` is the address for which the vault is redeeming its balance to, - walletPubKeyHash: The 20-byte wallet public key hash (computed using Bitcoin HASH160 over the compressed ECDSA public key), - mainUtxoTxHash: Data of the wallet's main UTXO TX hash, as currently known on the Ethereum chain, - mainUtxoTxOutputIndex: Data of the wallet's main UTXO output index, as currently known on Ethereum chain, - mainUtxoTxOutputValue: Data of the wallet's main UTXO output value, as currently known on Ethereum chain, - redeemerOutputScript The redeemer's length-prefixed output script (P2PKH, P2WPKH, P2SH or P2WSH) that will be used to lock redeemed BTC. |

### requestRedemption

```solidity
function requestRedemption(struct BridgeState.Storage self, bytes20 walletPubKeyHash, struct BitcoinTx.UTXO mainUtxo, address balanceOwner, address redeemer, bytes redeemerOutputScript, uint64 amount) internal
```

Requests redemption of the given amount from the specified
wallet to the redeemer Bitcoin output script.

Requirements:
- Wallet behind `walletPubKeyHash` must be live,
- `mainUtxo` components must point to the recent main UTXO
of the given wallet, as currently known on the Ethereum chain,
- `redeemerOutputScript` must be a proper Bitcoin script,
- `redeemerOutputScript` cannot have wallet PKH as payload,
- `amount` must be above or equal the `redemptionDustThreshold`,
- Given `walletPubKeyHash` and `redeemerOutputScript` pair can be
used for only one pending request at the same time,
- Wallet must have enough Bitcoin balance to proceed the request,
- Balance owner must make an allowance in the Bank that the Bridge
contract can spend the given `amount`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | The 20-byte wallet public key hash (computed using Bitcoin HASH160 over the compressed ECDSA public key). |
| mainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. |
| balanceOwner | address | The address of the Bank balance owner whose balance is getting redeemed. |
| redeemer | address | The Ethereum address of the redeemer who will be able to claim Bank balance if anything goes wrong during the redemption. In the most basic case, when someone redeems their Bitcoin balance from the Bank, `balanceOwner` is the same as `redeemer`. However, when a Vault is redeeming part of its balance for some redeemer address (for example, someone who has earlier deposited into that Vault), `balanceOwner` is the Vault, and `redeemer` is the address for which the vault is redeeming its balance to. |
| redeemerOutputScript | bytes | The redeemer's length-prefixed output script (P2PKH, P2WPKH, P2SH or P2WSH) that will be used to lock redeemed BTC. |
| amount | uint64 | Requested amount in satoshi. This is also the Bank balance that is taken from the `balanceOwner` upon request. Once the request is handled, the actual amount of BTC locked on the redeemer output script will be always lower than this value since the treasury and Bitcoin transaction fees must be incurred. The minimal amount satisfying the request can be computed as: `amount - (amount / redemptionTreasuryFeeDivisor) - redemptionTxMaxFee`. Fees values are taken at the moment of request creation. |

### submitRedemptionProof

```solidity
function submitRedemptionProof(struct BridgeState.Storage self, struct BitcoinTx.Info redemptionTx, struct BitcoinTx.Proof redemptionProof, struct BitcoinTx.UTXO mainUtxo, bytes20 walletPubKeyHash) external
```

Used by the wallet to prove the BTC redemption transaction
and to make the necessary bookkeeping. Redemption is only
accepted if it satisfies SPV proof.

The function is performing Bank balance updates by burning
the total redeemed Bitcoin amount from Bridge balance and
transferring the treasury fee sum to the treasury address.

It is possible to prove the given redemption only one time.

Requirements:
- `redemptionTx` components must match the expected structure. See
`BitcoinTx.Info` docs for reference. Their values must exactly
correspond to appropriate Bitcoin transaction fields to produce
a provable transaction hash,
- The `redemptionTx` should represent a Bitcoin transaction with
exactly 1 input that refers to the wallet's main UTXO. That
transaction should have 1..n outputs handling existing pending
redemption requests or pointing to reported timed out requests.
There can be also 1 optional output representing the
change and pointing back to the 20-byte wallet public key hash.
The change should be always present if the redeemed value sum
is lower than the total wallet's BTC balance,
- `redemptionProof` components must match the expected structure.
See `BitcoinTx.Proof` docs for reference. The `bitcoinHeaders`
field must contain a valid number of block headers, not less
than the `txProofDifficultyFactor` contract constant,
- `mainUtxo` components must point to the recent main UTXO
of the given wallet, as currently known on the Ethereum chain.
Additionally, the recent main UTXO on Ethereum must be set,
- `walletPubKeyHash` must be connected with the main UTXO used
as transaction single input.
Other remarks:
- Putting the change output as the first transaction output can
save some gas because the output processing loop begins each
iteration by checking whether the given output is the change
thus uses some gas for making the comparison. Once the change
is identified, that check is omitted in further iterations.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| redemptionTx | struct BitcoinTx.Info | Bitcoin redemption transaction data. |
| redemptionProof | struct BitcoinTx.Proof | Bitcoin redemption proof data. |
| mainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. |
| walletPubKeyHash | bytes20 | 20-byte public key hash (computed using Bitcoin HASH160 over the compressed ECDSA public key) of the wallet which performed the redemption transaction. |

### resolveRedeemingWallet

```solidity
function resolveRedeemingWallet(struct BridgeState.Storage self, bytes20 walletPubKeyHash, struct BitcoinTx.UTXO mainUtxo) internal view returns (struct Wallets.Wallet wallet)
```

Resolves redeeming wallet based on the provided wallet public
key hash. Validates the wallet state and current main UTXO, as
currently known on the Ethereum chain.

Requirements:
- Sweeping wallet must be either in Live or MovingFunds state,
- Main UTXO of the redeeming wallet must exists in the storage,
- The passed `mainUTXO` parameter must be equal to the stored one.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | public key hash of the wallet proving the sweep Bitcoin transaction. |
| mainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| wallet | struct Wallets.Wallet | Data of the sweeping wallet. |

### processRedemptionTxOutputs

```solidity
function processRedemptionTxOutputs(struct BridgeState.Storage self, bytes redemptionTxOutputVector, bytes20 walletPubKeyHash) internal returns (struct Redemption.RedemptionTxOutputsInfo info)
```

Processes the Bitcoin redemption transaction output vector.
It extracts each output and tries to identify it as a pending
redemption request, reported timed out request, or change.
Reverts if one of the outputs cannot be recognized properly.
This function also marks each request as processed by removing
them from `pendingRedemptions` mapping.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| redemptionTxOutputVector | bytes | Bitcoin redemption transaction output vector. This function assumes vector's structure is valid so it must be validated using e.g. `BTCUtils.validateVout` function before it is passed here. |
| walletPubKeyHash | bytes20 | 20-byte public key hash (computed using Bitcoin HASH160 over the compressed ECDSA public key) of the wallet which performed the redemption transaction. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| info | struct Redemption.RedemptionTxOutputsInfo | Outcomes of the processing. |

### processRedemptionTxOutputs

```solidity
function processRedemptionTxOutputs(struct BridgeState.Storage self, bytes redemptionTxOutputVector, bytes20 walletPubKeyHash, struct Redemption.RedemptionTxOutputsProcessingInfo processInfo) internal returns (struct Redemption.RedemptionTxOutputsInfo resultInfo)
```

Processes all outputs from the redemption transaction. Tries to
identify output as a change output, pending redemption request
or reported redemption. Reverts if one of the outputs cannot be
recognized properly. Marks each request as processed by removing
them from `pendingRedemptions` mapping.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| redemptionTxOutputVector | bytes | Bitcoin redemption transaction output vector. This function assumes vector's structure is valid so it must be validated using e.g. `BTCUtils.validateVout` function before it is passed here. |
| walletPubKeyHash | bytes20 | 20-byte public key hash (computed using Bitcoin HASH160 over the compressed ECDSA public key) of the wallet which performed the redemption transaction. |
| processInfo | struct Redemption.RedemptionTxOutputsProcessingInfo | RedemptionTxOutputsProcessingInfo identifying output starting index, the number of outputs and possible wallet change P2PKH and P2WPKH scripts. |

### processNonChangeRedemptionTxOutput

```solidity
function processNonChangeRedemptionTxOutput(struct BridgeState.Storage self, uint256 redemptionKey, uint64 outputValue) internal returns (uint64 burnableValue, uint64 treasuryFee)
```

Processes a single redemption transaction output. Tries to
identify output as a pending redemption request or reported
redemption timeout. Output script passed to this function must
not be the change output. Such output needs to be identified
separately before calling this function.
Reverts if output is neither requested pending redemption nor
requested and reported timed-out redemption.
This function also marks each pending request as processed by
removing them from `pendingRedemptions` mapping.

Requirements:
- This function should be called only if the given output
represents redemption. It must not be the change output.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| redemptionKey | uint256 | Redemption key of the output being processed. |
| outputValue | uint64 | Value of the output being processed. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| burnableValue | uint64 | The value burnable as a result of processing this single redemption output. This value needs to be summed up with burnable values of all other outputs to evaluate total burnable value for the entire redemption transaction. This value is 0 for a timed-out redemption request. |
| treasuryFee | uint64 | The treasury fee from this single redemption output. This value needs to be summed up with treasury fees of all other outputs to evaluate the total treasury fee for the entire redemption transaction. This value is 0 for a timed-out redemption request. |

### notifyRedemptionTimeout

```solidity
function notifyRedemptionTimeout(struct BridgeState.Storage self, bytes20 walletPubKeyHash, uint32[] walletMembersIDs, bytes redeemerOutputScript) external
```

Notifies that there is a pending redemption request associated
with the given wallet, that has timed out. The redemption
request is identified by the key built as
`keccak256(keccak256(redeemerOutputScript) | walletPubKeyHash)`.
The results of calling this function:
- the pending redemptions value for the wallet will be decreased
by the requested amount (minus treasury fee),
- the tokens taken from the redeemer on redemption request will
be returned to the redeemer,
- the request will be moved from pending redemptions to
timed-out redemptions,
- if the state of the wallet is `Live` or `MovingFunds`, the
wallet operators will be slashed and the notifier will be
rewarded,
- if the state of wallet is `Live`, the wallet will be closed or
marked as `MovingFunds` (depending on the presence or absence
of the wallet's main UTXO) and the wallet will no longer be
marked as the active wallet (if it was marked as such).

Requirements:
- The wallet must be in the Live or MovingFunds or Terminated state,
- The redemption request identified by `walletPubKeyHash` and
`redeemerOutputScript` must exist,
- The expression `keccak256(abi.encode(walletMembersIDs))` must
be exactly the same as the hash stored under `membersIdsHash`
for the given `walletID`. Those IDs are not directly stored
in the contract for gas efficiency purposes but they can be
read from appropriate `DkgResultSubmitted` and `DkgResultApproved`
events of the `WalletRegistry` contract,
- The amount of time defined by `redemptionTimeout` must have
passed since the redemption was requested (the request must be
timed-out).

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members. |
| redeemerOutputScript | bytes | The redeemer's length-prefixed output script (P2PKH, P2WPKH, P2SH or P2WSH). |

### getRedemptionKey

```solidity
function getRedemptionKey(bytes20 walletPubKeyHash, bytes script) internal pure returns (uint256)
```

Calculate redemption key without allocations.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletPubKeyHash | bytes20 | the pubkey hash of the wallet. |
| script | bytes | the output script of the redemption. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | The key = keccak256(keccak256(script) | walletPubKeyHash). |

### _getRedemptionKey

```solidity
function _getRedemptionKey(bytes20 walletPubKeyHash, bytes32 scriptHash) internal pure returns (uint256)
```

Finish calculating redemption key without allocations.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletPubKeyHash | bytes20 | the pubkey hash of the wallet. |
| scriptHash | bytes32 | the output script hash of the redemption. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | The key = keccak256(scriptHash | walletPubKeyHash). |

