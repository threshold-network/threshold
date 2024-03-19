# Solidity API

## Deposit

The library handles the logic for revealing Bitcoin deposits to
the Bridge.

The depositor puts together a P2SH or P2WSH address to deposit the
funds. This script is unique to each depositor and looks like this:

```
<depositorAddress> DROP
<blindingFactor> DROP
DUP HASH160 <walletPubKeyHash> EQUAL
IF
CHECKSIG
ELSE
DUP HASH160 <refundPubkeyHash> EQUALVERIFY
<refundLocktime> CHECKLOCKTIMEVERIFY DROP
CHECKSIG
ENDIF
```

Since each depositor has their own Ethereum address and their own
blinding factor, each depositor’s script is unique, and the hash
of each depositor’s script is unique.

This library also supports another variant of the deposit script
allowing to embed 32-byte extra data. The extra data allows to attach
additional context to the deposit. The script with 32-byte extra data
looks like this:

```
<depositorAddress> DROP
<extraData> DROP
<blindingFactor> DROP
DUP HASH160 <walletPubKeyHash> EQUAL
IF
CHECKSIG
ELSE
DUP HASH160 <refundPubkeyHash> EQUALVERIFY
<refundLocktime> CHECKLOCKTIMEVERIFY DROP
CHECKSIG
ENDIF
```

### DepositRevealInfo

```solidity
struct DepositRevealInfo {
  uint32 fundingOutputIndex;
  bytes8 blindingFactor;
  bytes20 walletPubKeyHash;
  bytes20 refundPubKeyHash;
  bytes4 refundLocktime;
  address vault;
}
```

### DepositRequest

```solidity
struct DepositRequest {
  address depositor;
  uint64 amount;
  uint32 revealedAt;
  address vault;
  uint64 treasuryFee;
  uint32 sweptAt;
  bytes32 extraData;
}
```

### DepositRevealed

```solidity
event DepositRevealed(bytes32 fundingTxHash, uint32 fundingOutputIndex, address depositor, uint64 amount, bytes8 blindingFactor, bytes20 walletPubKeyHash, bytes20 refundPubKeyHash, bytes4 refundLocktime, address vault)
```

### revealDeposit

```solidity
function revealDeposit(struct BridgeState.Storage self, struct BitcoinTx.Info fundingTx, struct Deposit.DepositRevealInfo reveal) external
```

Used by the depositor to reveal information about their P2(W)SH
Bitcoin deposit to the Bridge on Ethereum chain. The off-chain
wallet listens for revealed deposit events and may decide to
include the revealed deposit in the next executed sweep.
Information about the Bitcoin deposit can be revealed before or
after the Bitcoin transaction with P2(W)SH deposit is mined on
the Bitcoin chain. Worth noting, the gas cost of this function
scales with the number of P2(W)SH transaction inputs and
outputs. The deposit may be routed to one of the trusted vaults.
When a deposit is routed to a vault, vault gets notified when
the deposit gets swept and it may execute the appropriate action.

Requirements:
- This function must be called by the same Ethereum address as the
one used in the P2(W)SH BTC deposit transaction as a depositor,
- `reveal.walletPubKeyHash` must identify a `Live` wallet,
- `reveal.vault` must be 0x0 or point to a trusted vault,
- `reveal.fundingOutputIndex` must point to the actual P2(W)SH
output of the BTC deposit transaction,
- `reveal.blindingFactor` must be the blinding factor used in the
P2(W)SH BTC deposit transaction,
- `reveal.walletPubKeyHash` must be the wallet pub key hash used in
the P2(W)SH BTC deposit transaction,
- `reveal.refundPubKeyHash` must be the refund pub key hash used in
the P2(W)SH BTC deposit transaction,
- `reveal.refundLocktime` must be the refund locktime used in the
P2(W)SH BTC deposit transaction,
- BTC deposit for the given `fundingTxHash`, `fundingOutputIndex`
can be revealed only one time.

If any of these requirements is not met, the wallet _must_ refuse
to sweep the deposit and the depositor has to wait until the
deposit script unlocks to receive their BTC back.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| fundingTx | struct BitcoinTx.Info | Bitcoin funding transaction data, see `BitcoinTx.Info`. |
| reveal | struct Deposit.DepositRevealInfo | Deposit reveal data, see `RevealInfo struct. |

### _revealDeposit

```solidity
function _revealDeposit(struct BridgeState.Storage self, struct BitcoinTx.Info fundingTx, struct Deposit.DepositRevealInfo reveal, bytes32 extraData) internal
```

Internal function encapsulating the core logic of the deposit
reveal process. Handles both regular deposits without extra data
as well as deposits with 32-byte extra data embedded in the
deposit script. The behavior is controlled by the `extraData`
parameter. If `extraData` is bytes32(0), the function triggers
the flow for regular deposits. If `extraData` is not bytes32(0),
the function triggers the flow for deposits with 32-byte
extra data.

Requirements are described in the docstrings of `revealDeposit` and
`revealDepositWithExtraData` external functions.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| fundingTx | struct BitcoinTx.Info | Bitcoin funding transaction data, see `BitcoinTx.Info`. |
| reveal | struct Deposit.DepositRevealInfo | Deposit reveal data, see `RevealInfo struct. |
| extraData | bytes32 | 32-byte deposit extra data. Can be bytes32(0). |

### _emitDepositRevealedEvent

```solidity
function _emitDepositRevealedEvent(bytes32 fundingTxHash, uint64 fundingOutputAmount, struct Deposit.DepositRevealInfo reveal) internal
```

Emits the `DepositRevealed` event.

This function is extracted to overcome the stack too deep error.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| fundingTxHash | bytes32 | The funding transaction hash. |
| fundingOutputAmount | uint64 | The funding output amount in satoshi. |
| reveal | struct Deposit.DepositRevealInfo | Deposit reveal data, see `RevealInfo struct. |

### revealDepositWithExtraData

```solidity
function revealDepositWithExtraData(struct BridgeState.Storage self, struct BitcoinTx.Info fundingTx, struct Deposit.DepositRevealInfo reveal, bytes32 extraData) external
```

Sibling of the `revealDeposit` function. This function allows
to reveal a P2(W)SH Bitcoin deposit with 32-byte extra data
embedded in the deposit script. The extra data allows to
attach additional context to the deposit. For example,
it allows a third-party smart contract to reveal the
deposit on behalf of the original depositor and provide
additional services once the deposit is handled. In this
case, the address of the original depositor can be encoded
as extra data.

Requirements:
- All requirements from `revealDeposit` function must be met,
- `extraData` must not be bytes32(0),
- `extraData` must be the actual extra data used in the P2(W)SH
BTC deposit transaction.

If any of these requirements is not met, the wallet _must_ refuse
to sweep the deposit and the depositor has to wait until the
deposit script unlocks to receive their BTC back.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| fundingTx | struct BitcoinTx.Info | Bitcoin funding transaction data, see `BitcoinTx.Info`. |
| reveal | struct Deposit.DepositRevealInfo | Deposit reveal data, see `RevealInfo struct. |
| extraData | bytes32 | 32-byte deposit extra data. |

### validateDepositRefundLocktime

```solidity
function validateDepositRefundLocktime(struct BridgeState.Storage self, bytes4 refundLocktime) internal view
```

Validates the deposit refund locktime. The validation passes
successfully only if the deposit reveal is done respectively
earlier than the moment when the deposit refund locktime is
reached, i.e. the deposit become refundable. Reverts otherwise.

Requirements:
- `refundLocktime` as integer must be >= 500M
- `refundLocktime` must denote a timestamp that is at least
`depositRevealAheadPeriod` seconds later than the moment
of `block.timestamp`

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| refundLocktime | bytes4 | The deposit refund locktime as 4-byte LE. |

