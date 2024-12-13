# Solidity API

## L1BitcoinDepositor

This contract is part of the direct bridging mechanism allowing
users to obtain ERC20 TBTC on supported L2 chains, without the need
to interact with the L1 tBTC ledger chain where minting occurs.

`L1BitcoinDepositor` is deployed on the L1 chain and interacts with
their L2 counterpart, the `L2BitcoinDepositor`, deployed on the given
L2 chain. Each `L1BitcoinDepositor` & `L2BitcoinDepositor` pair is
responsible for a specific L2 chain.

The outline of the direct bridging mechanism is as follows:
1. An L2 user issues a Bitcoin funding transaction to a P2(W)SH
deposit address that embeds the `L1BitcoinDepositor` contract
and L2 user addresses. The `L1BitcoinDepositor` contract serves
as the actual depositor on the L1 chain while the L2 user
address is set as the deposit owner who will receive the
minted ERC20 TBTC.
2. The data about the Bitcoin funding transaction and deposit
address are passed to the relayer. In the first iteration of
the direct bridging mechanism, this is achieved using an
on-chain event emitted by the `L2BitcoinDepositor` contract.
Further iterations assumes those data are passed off-chain, e.g.
through a REST API exposed by the relayer.
3. The relayer uses the data to initialize a deposit on the L1
chain by calling the `initializeDeposit` function of the
`L1BitcoinDepositor` contract. The `initializeDeposit` function
reveals the deposit to the tBTC Bridge so minting of ERC20 L1 TBTC
can occur.
4. Once minting is complete, the `L1BitcoinDepositor` contract
receives minted ERC20 L1 TBTC. The relayer then calls the
`finalizeDeposit` function of the `L1BitcoinDepositor` contract
to transfer the minted ERC20 L1 TBTC to the L2 user address. This
is achieved using the Wormhole protocol. First, the `finalizeDeposit`
function initiates a Wormhole token transfer that locks the ERC20
L1 TBTC within the Wormhole Token Bridge contract and assigns
Wormhole-wrapped L2 TBTC to the corresponding `L2WormholeGateway`
contract. Then, `finalizeDeposit` notifies the `L2BitcoinDepositor`
contract by sending a Wormhole message containing the VAA
of the Wormhole token transfer. The `L2BitcoinDepositor` contract
receives the Wormhole message, and calls the `L2WormholeGateway`
contract that redeems Wormhole-wrapped L2 TBTC from the Wormhole
Token Bridge and uses it to mint canonical L2 TBTC to the L2 user
address.

### DepositState

```solidity
enum DepositState {
  Unknown,
  Initialized,
  Finalized
}
```

### GasReimbursement

```solidity
struct GasReimbursement {
  address receiver;
  uint96 gasSpent;
}
```

### deposits

```solidity
mapping(uint256 => enum L1BitcoinDepositor.DepositState) deposits
```

Holds the deposit state, keyed by the deposit key calculated for
the individual deposit during the call to `initializeDeposit`
function.

### tbtcToken

```solidity
contract IERC20Upgradeable tbtcToken
```

ERC20 L1 TBTC token contract.

### wormhole

```solidity
contract IWormhole wormhole
```

`Wormhole` core contract on L1.

### wormholeRelayer

```solidity
contract IWormholeRelayer wormholeRelayer
```

`WormholeRelayer` contract on L1.

### wormholeTokenBridge

```solidity
contract IWormholeTokenBridge wormholeTokenBridge
```

Wormhole `TokenBridge` contract on L1.

### l2WormholeGateway

```solidity
address l2WormholeGateway
```

tBTC `L2WormholeGateway` contract on the corresponding L2 chain.

### l2ChainId

```solidity
uint16 l2ChainId
```

Wormhole chain ID of the corresponding L2 chain.

### l2BitcoinDepositor

```solidity
address l2BitcoinDepositor
```

tBTC `L2BitcoinDepositor` contract on the corresponding L2 chain.

### l2FinalizeDepositGasLimit

```solidity
uint256 l2FinalizeDepositGasLimit
```

Gas limit necessary to execute the L2 part of the deposit
finalization. This value is used to calculate the payment for
the Wormhole Relayer that is responsible to execute the
deposit finalization on the corresponding L2 chain. Can be
updated by the owner.

### gasReimbursements

```solidity
mapping(uint256 => struct L1BitcoinDepositor.GasReimbursement) gasReimbursements
```

Holds deferred gas reimbursements for deposit initialization
(indexed by deposit key). Reimbursement for deposit
initialization is paid out upon deposit finalization. This is
because the tBTC Bridge accepts all (even invalid) deposits but
mints ERC20 TBTC only for the valid ones. Paying out the
reimbursement directly upon initialization would make the
reimbursement pool vulnerable to malicious actors that could
drain it by initializing invalid deposits.

### initializeDepositGasOffset

```solidity
uint256 initializeDepositGasOffset
```

Gas that is meant to balance the overall cost of deposit initialization.
Can be updated by the owner based on the current market conditions.

### finalizeDepositGasOffset

```solidity
uint256 finalizeDepositGasOffset
```

Gas that is meant to balance the overall cost of deposit finalization.
Can be updated by the owner based on the current market conditions.

### reimbursementAuthorizations

```solidity
mapping(address => bool) reimbursementAuthorizations
```

Set of addresses that are authorized to receive gas reimbursements
for deposit initialization and finalization. The authorization is
granted by the contract owner.

### DepositInitialized

```solidity
event DepositInitialized(uint256 depositKey, address l2DepositOwner, address l1Sender)
```

### DepositFinalized

```solidity
event DepositFinalized(uint256 depositKey, address l2DepositOwner, address l1Sender, uint256 initialAmount, uint256 tbtcAmount)
```

### L2FinalizeDepositGasLimitUpdated

```solidity
event L2FinalizeDepositGasLimitUpdated(uint256 l2FinalizeDepositGasLimit)
```

### GasOffsetParametersUpdated

```solidity
event GasOffsetParametersUpdated(uint256 initializeDepositGasOffset, uint256 finalizeDepositGasOffset)
```

### ReimbursementAuthorizationUpdated

```solidity
event ReimbursementAuthorizationUpdated(address _address, bool authorization)
```

### onlyReimbursableAdmin

```solidity
modifier onlyReimbursableAdmin()
```

This modifier comes from the `Reimbursable` base contract and
must be overridden to protect the `updateReimbursementPool` call.

### constructor

```solidity
constructor() public
```

### initialize

```solidity
function initialize(address _tbtcBridge, address _tbtcVault, address _wormhole, address _wormholeRelayer, address _wormholeTokenBridge, address _l2WormholeGateway, uint16 _l2ChainId) external
```

### attachL2BitcoinDepositor

```solidity
function attachL2BitcoinDepositor(address _l2BitcoinDepositor) external
```

Sets the address of the `L2BitcoinDepositor` contract on the
corresponding L2 chain. This function solves the chicken-and-egg
problem of setting the `L2BitcoinDepositor` contract address
on the `L1BitcoinDepositor` contract and vice versa.

Requirements:
- Can be called only by the contract owner,
- The address must not be set yet,
- The new address must not be 0x0.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _l2BitcoinDepositor | address | Address of the `L2BitcoinDepositor` contract. |

### updateL2FinalizeDepositGasLimit

```solidity
function updateL2FinalizeDepositGasLimit(uint256 _l2FinalizeDepositGasLimit) external
```

Updates the gas limit necessary to execute the L2 part of the
deposit finalization.

Requirements:
- Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _l2FinalizeDepositGasLimit | uint256 | New gas limit. |

### updateGasOffsetParameters

```solidity
function updateGasOffsetParameters(uint256 _initializeDepositGasOffset, uint256 _finalizeDepositGasOffset) external
```

Updates the values of gas offset parameters.

Can be called only by the contract owner. The caller is responsible
for validating parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _initializeDepositGasOffset | uint256 | New initialize deposit gas offset. |
| _finalizeDepositGasOffset | uint256 | New finalize deposit gas offset. |

### updateReimbursementAuthorization

```solidity
function updateReimbursementAuthorization(address _address, bool authorization) external
```

Updates the reimbursement authorization for the given address.

Requirements:
- Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _address | address | Address to update the authorization for. |
| authorization | bool | New authorization status. |

### initializeDeposit

```solidity
function initializeDeposit(struct IBridgeTypes.BitcoinTxInfo fundingTx, struct IBridgeTypes.DepositRevealInfo reveal, address l2DepositOwner) external
```

Initializes the deposit process on L1 by revealing the deposit
data (funding transaction and components of the P2(W)SH deposit
address) to the tBTC Bridge. Once tBTC minting is completed,
this call should be followed by a call to `finalizeDeposit`.
Callers of `initializeDeposit` are eligible for a gas refund
that is paid out upon deposit finalization (only if the
reimbursement pool is attached and the given caller is
authorized for refunds).

The Bitcoin funding transaction must transfer funds to a P2(W)SH
deposit address whose underlying script is built from the
following components:

<depositor-address> DROP
<depositor-extra-data> DROP
<blinding-factor> DROP
DUP HASH160 <signingGroupPubkeyHash> EQUAL
IF
CHECKSIG
ELSE
DUP HASH160 <refundPubkeyHash> EQUALVERIFY
<locktime> CHECKLOCKTIMEVERIFY DROP
CHECKSIG
ENDIF

Where:

<depositor-address> 20-byte L1 address of the
`L1BitcoinDepositor` contract.

<depositor-extra-data> L2 deposit owner address in the Wormhole
format, i.e. 32-byte value left-padded with 0.

<blinding-factor> 8-byte deposit blinding factor, as used in the
tBTC bridge.

<signingGroupPubkeyHash> The compressed Bitcoin public key (33
bytes and 02 or 03 prefix) of the deposit's wallet hashed in the
HASH160 Bitcoin opcode style. This must point to the active tBTC
bridge wallet.

<refundPubkeyHash> The compressed Bitcoin public key (33 bytes
and 02 or 03 prefix) that can be used to make the deposit refund
after the tBTC bridge refund locktime passed. Hashed in the
HASH160 Bitcoin opcode style. This is needed only as a security
measure protecting the user in case tBTC bridge completely stops
functioning.

<locktime> The Bitcoin script refund locktime (4-byte LE),
according to tBTC bridge rules.

Please consult tBTC `Bridge.revealDepositWithExtraData` function
documentation for more information.

Requirements:
- The L2 deposit owner address must not be 0x0,
- The function can be called only one time for the given Bitcoin
funding transaction,
- The L2 deposit owner must be embedded in the Bitcoin P2(W)SH
deposit script as the <depositor-extra-data> field. The 20-byte
address must be expressed as a 32-byte value left-padded with 0.
If the value in the Bitcoin script and the value passed as
parameter do not match, the function will revert,
- All the requirements of tBTC Bridge.revealDepositWithExtraData
must be met.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| fundingTx | struct IBridgeTypes.BitcoinTxInfo | Bitcoin funding transaction data. |
| reveal | struct IBridgeTypes.DepositRevealInfo | Deposit reveal data. |
| l2DepositOwner | address | Address of the L2 deposit owner. |

### finalizeDeposit

```solidity
function finalizeDeposit(uint256 depositKey) external payable
```

Finalizes the deposit process by transferring ERC20 L1 TBTC
to the L2 deposit owner. This function should be called after
the deposit was initialized with a call to `initializeDeposit`
function and after ERC20 L1 TBTC was minted by the tBTC Bridge
to the `L1BitcoinDepositor` contract. Please note several hours
may pass between `initializeDeposit`and `finalizeDeposit`.
If the reimbursement pool is attached, the function pays out
a gas and call's value refund to the caller (if the given
caller is authorized for refunds) as well as the deferred gas
refund to the caller of `initializeDeposit` corresponding to
the finalized deposit.

Requirements:
- `initializeDeposit` was called for the given deposit before,
- ERC20 L1 TBTC was minted by tBTC Bridge to this contract,
- The function was not called for the given deposit before,
- The call must carry a payment for the Wormhole Relayer that
is responsible for executing the deposit finalization on the
corresponding L2 chain. The payment must be equal to the
value returned by the `quoteFinalizeDeposit` function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| depositKey | uint256 | The deposit key, as emitted in the `DepositInitialized` event emitted by the `initializeDeposit` function for the deposit. |

### _refundToGasSpent

```solidity
function _refundToGasSpent(uint256 refund) internal returns (uint256)
```

The `ReimbursementPool` contract issues refunds based on
gas spent. If there is a need to get a specific refund based
on WEI value, such a value must be first converted to gas spent.
This function does such a conversion.

This function is the reverse of the logic used
within `ReimbursementPool.refund`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| refund | uint256 | Refund value in WEI. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Refund value as gas spent. |

### quoteFinalizeDeposit

```solidity
function quoteFinalizeDeposit() external view returns (uint256 cost)
```

Quotes the payment that must be attached to the `finalizeDeposit`
function call. The payment is necessary to cover the cost of
the Wormhole Relayer that is responsible for executing the
deposit finalization on the corresponding L2 chain.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| cost | uint256 | The cost of the `finalizeDeposit` function call in WEI. |

### _quoteFinalizeDeposit

```solidity
function _quoteFinalizeDeposit(uint256 messageFee) internal view returns (uint256 cost)
```

Internal version of the `quoteFinalizeDeposit` function that
works with a custom Wormhole message fee.

Implemented based on examples presented as part of the Wormhole SDK:
https://github.com/wormhole-foundation/hello-token/blob/8ec757248788dc12183f13627633e1d6fd1001bb/src/example-extensions/HelloTokenWithoutSDK.sol#L23

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| messageFee | uint256 | Custom Wormhole message fee. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| cost | uint256 | The cost of the `finalizeDeposit` function call in WEI. |

### _transferTbtc

```solidity
function _transferTbtc(uint256 amount, bytes32 l2Receiver) internal
```

Transfers ERC20 L1 TBTC to the L2 deposit owner using the Wormhole
protocol. The function initiates a Wormhole token transfer that
locks the ERC20 L1 TBTC within the Wormhole Token Bridge contract
and assigns Wormhole-wrapped L2 TBTC to the corresponding
`L2WormholeGateway` contract. Then, the function notifies the
`L2BitcoinDepositor` contract by sending a Wormhole message
containing the VAA of the Wormhole token transfer. The
`L2BitcoinDepositor` contract receives the Wormhole message,
and calls the `L2WormholeGateway` contract that redeems
Wormhole-wrapped L2 TBTC from the Wormhole Token Bridge and
uses it to mint canonical L2 TBTC to the L2 deposit owner address.

Requirements:
- The normalized amount (1e8 precision) must be greater than 0,
- The appropriate payment for the Wormhole Relayer must be
attached to the call (as calculated by `quoteFinalizeDeposit`).
Implemented based on examples presented as part of the Wormhole SDK:
https://github.com/wormhole-foundation/hello-token/blob/8ec757248788dc12183f13627633e1d6fd1001bb/src/example-extensions/HelloTokenWithoutSDK.sol#L29

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | Amount of TBTC L1 ERC20 to transfer (1e18 precision). |
| l2Receiver | bytes32 | Address of the L2 deposit owner. |

