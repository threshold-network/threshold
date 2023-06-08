# Solidity API
## Table Of Contents

<!-- toc -->

- [Bank](#bank)
- [IReceiveBalanceApproval](#ireceivebalanceapproval)
- [BitcoinTx](#bitcointx)
- [Bridge](#bridge)
- [BridgeGovernance](#bridgegovernance)
- [BridgeGovernanceParameters](#bridgegovernanceparameters)
- [BridgeState](#bridgestate)
- [Deposit](#deposit)
- [DepositSweep](#depositsweep)
- [EcdsaLib](#ecdsalib)
- [Fraud](#fraud)
- [Heartbeat](#heartbeat)
- [IRelay](#irelay)
- [MovingFunds](#movingfunds)
- [OutboundTx](#outboundtx)
- [Redemption](#redemption)
- [Wallets](#wallets)
- [IVault](#ivault)
- [GovernanceUtils](#governanceutils)
- [VendingMachine](#vendingmachine)
- [VendingMachineV2](#vendingmachinev2)
- [VendingMachineV3](#vendingmachinev3)
- [WalletCoordinator](#walletcoordinator)
- [L2TBTC](#l2tbtc)
- [IWormholeTokenBridge](#iwormholetokenbridge)
- [L2WormholeGateway](#l2wormholegateway)
- [MaintainerProxy](#maintainerproxy)
- [Epoch](#epoch)
- [ILightRelay](#ilightrelay)
- [RelayUtils](#relayutils)
- [LightRelay](#lightrelay)
- [LightRelayMaintainerProxy](#lightrelaymaintainerproxy)
- [TBTC](#tbtc)
- [DonationVault](#donationvault)
- [TBTCOptimisticMinting](#tbtcoptimisticminting)
- [TBTCVault](#tbtcvault)

<!-- tocstop -->

## Bank

Bank is a central component tracking Bitcoin balances. Balances can
be transferred between balance owners, and balance owners can
approve their balances to be spent by others. Balances in the Bank
are updated for depositors who deposited their Bitcoin into the
Bridge and only the Bridge can increase balances.

Bank is a governable contract and the Governance can upgrade the Bridge
address.

### bridge

```solidity
address bridge
```

### balanceOf

```solidity
mapping(address => uint256) balanceOf
```

The balance of the given account in the Bank. Zero by default.

### allowance

```solidity
mapping(address => mapping(address => uint256)) allowance
```

The remaining amount of balance a spender will be
allowed to transfer on behalf of an owner using
`transferBalanceFrom`. Zero by default.

### nonces

```solidity
mapping(address => uint256) nonces
```

Returns the current nonce for an EIP2612 permission for the
provided balance owner to protect against replay attacks. Used
to construct an EIP2612 signature provided to the `permit`
function.

### cachedChainId

```solidity
uint256 cachedChainId
```

### cachedDomainSeparator

```solidity
bytes32 cachedDomainSeparator
```

### PERMIT_TYPEHASH

```solidity
bytes32 PERMIT_TYPEHASH
```

Returns an EIP2612 Permit message hash. Used to construct
an EIP2612 signature provided to the `permit` function.

### BalanceTransferred

```solidity
event BalanceTransferred(address from, address to, uint256 amount)
```

### BalanceApproved

```solidity
event BalanceApproved(address owner, address spender, uint256 amount)
```

### BalanceIncreased

```solidity
event BalanceIncreased(address owner, uint256 amount)
```

### BalanceDecreased

```solidity
event BalanceDecreased(address owner, uint256 amount)
```

### BridgeUpdated

```solidity
event BridgeUpdated(address newBridge)
```

### onlyBridge

```solidity
modifier onlyBridge()
```

### constructor

```solidity
constructor() public
```

### updateBridge

```solidity
function updateBridge(address _bridge) external
```

Allows the Governance to upgrade the Bridge address.

The function does not implement any governance delay and does not
check the status of the Bridge. The Governance implementation needs
to ensure all requirements for the upgrade are satisfied before
executing this function.
Requirements:
- The new Bridge address must not be zero.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _bridge | address | The new Bridge address. |

### transferBalance

```solidity
function transferBalance(address recipient, uint256 amount) external
```

Moves the given `amount` of balance from the caller to
`recipient`.

Requirements:
- `recipient` cannot be the zero address,
- the caller must have a balance of at least `amount`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| recipient | address | The recipient of the balance. |
| amount | uint256 | The amount of the balance transferred. |

### approveBalance

```solidity
function approveBalance(address spender, uint256 amount) external
```

Sets `amount` as the allowance of `spender` over the caller's
balance. Does not allow updating an existing allowance to
a value that is non-zero to avoid someone using both the old and
the new allowance by unfortunate transaction ordering. To update
an allowance to a non-zero value please set it to zero first or
use `increaseBalanceAllowance` or `decreaseBalanceAllowance` for
an atomic update.

If the `amount` is set to `type(uint256).max`,
`transferBalanceFrom` will not reduce an allowance.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| spender | address | The address that will be allowed to spend the balance. |
| amount | uint256 | The amount the spender is allowed to spend. |

### approveBalanceAndCall

```solidity
function approveBalanceAndCall(address spender, uint256 amount, bytes extraData) external
```

Sets the `amount` as an allowance of a smart contract `spender`
over the caller's balance and calls the `spender` via
`receiveBalanceApproval`.

If the `amount` is set to `type(uint256).max`, the potential
`transferBalanceFrom` executed in `receiveBalanceApproval` of
`spender` will not reduce an allowance. Beware that changing an
allowance with this function brings the risk that `spender` may use
both the old and the new allowance by unfortunate transaction
ordering. Please use `increaseBalanceAllowance` and
`decreaseBalanceAllowance` to eliminate the risk.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| spender | address | The smart contract that will be allowed to spend the balance. |
| amount | uint256 | The amount the spender contract is allowed to spend. |
| extraData | bytes | Extra data passed to the `spender` contract via `receiveBalanceApproval` call. |

### increaseBalanceAllowance

```solidity
function increaseBalanceAllowance(address spender, uint256 addedValue) external
```

Atomically increases the caller's balance allowance granted to
`spender` by the given `addedValue`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| spender | address | The spender address for which the allowance is increased. |
| addedValue | uint256 | The amount by which the allowance is increased. |

### decreaseBalanceAllowance

```solidity
function decreaseBalanceAllowance(address spender, uint256 subtractedValue) external
```

Atomically decreases the caller's balance allowance granted to
`spender` by the given `subtractedValue`.

Requirements:
- `spender` must not be the zero address,
- the current allowance for `spender` must not be lower than
the `subtractedValue`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| spender | address | The spender address for which the allowance is decreased. |
| subtractedValue | uint256 | The amount by which the allowance is decreased. |

### transferBalanceFrom

```solidity
function transferBalanceFrom(address spender, address recipient, uint256 amount) external
```

Moves `amount` of balance from `spender` to `recipient` using the
allowance mechanism. `amount` is then deducted from the caller's
allowance unless the allowance was made for `type(uint256).max`.

Requirements:
- `recipient` cannot be the zero address,
- `spender` must have a balance of at least `amount`,
- the caller must have an allowance for `spender`'s balance of at
least `amount`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| spender | address | The address from which the balance is transferred. |
| recipient | address | The address to which the balance is transferred. |
| amount | uint256 | The amount of balance that is transferred. |

### permit

```solidity
function permit(address owner, address spender, uint256 amount, uint256 deadline, uint8 v, bytes32 r, bytes32 s) external
```

An EIP2612 approval made with secp256k1 signature. Users can
authorize a transfer of their balance with a signature
conforming to the EIP712 standard, rather than an on-chain
transaction from their address. Anyone can submit this signature
on the user's behalf by calling the `permit` function, paying
gas fees, and possibly performing other actions in the same
transaction.

The deadline argument can be set to `type(uint256).max to create
permits that effectively never expire.  If the `amount` is set
to `type(uint256).max` then `transferBalanceFrom` will not
reduce an allowance. Beware that changing an allowance with this
function brings the risk that someone may use both the old and the
new allowance by unfortunate transaction ordering. Please use
`increaseBalanceAllowance` and `decreaseBalanceAllowance` to
eliminate the risk.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| owner | address | The balance owner who signed the permission. |
| spender | address | The address that will be allowed to spend the balance. |
| amount | uint256 | The amount the spender is allowed to spend. |
| deadline | uint256 | The UNIX time until which the permit is valid. |
| v | uint8 | V part of the permit signature. |
| r | bytes32 | R part of the permit signature. |
| s | bytes32 | S part of the permit signature. |

### increaseBalances

```solidity
function increaseBalances(address[] recipients, uint256[] amounts) external
```

Increases balances of the provided `recipients` by the provided
`amounts`. Can only be called by the Bridge.

Requirements:
- length of `recipients` and `amounts` must be the same,
- none of `recipients` addresses must point to the Bank.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| recipients | address[] | Balance increase recipients. |
| amounts | uint256[] | Amounts by which balances are increased. |

### increaseBalance

```solidity
function increaseBalance(address recipient, uint256 amount) external
```

Increases balance of the provided `recipient` by the provided
`amount`. Can only be called by the Bridge.

Requirements:
- `recipient` address must not point to the Bank.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| recipient | address | Balance increase recipient. |
| amount | uint256 | Amount by which the balance is increased. |

### increaseBalanceAndCall

```solidity
function increaseBalanceAndCall(address vault, address[] recipients, uint256[] amounts) external
```

Increases the given smart contract `vault`'s balance and
notifies the `vault` contract about it.
Can be called only by the Bridge.

Requirements:
- `vault` must implement `IVault` interface,
- length of `recipients` and `amounts` must be the same.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| vault | address | Address of `IVault` recipient contract. |
| recipients | address[] | Balance increase recipients. |
| amounts | uint256[] | Amounts by which balances are increased. |

### decreaseBalance

```solidity
function decreaseBalance(uint256 amount) external
```

Decreases caller's balance by the provided `amount`. There is no
way to restore the balance so do not call this function unless
you really know what you are doing!

Requirements:
- The caller must have a balance of at least `amount`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | The amount by which the balance is decreased. |

### DOMAIN_SEPARATOR

```solidity
function DOMAIN_SEPARATOR() public view returns (bytes32)
```

Returns hash of EIP712 Domain struct with `TBTC Bank` as
a signing domain and Bank contract as a verifying contract.
Used to construct an EIP2612 signature provided to the `permit`
function.

### _increaseBalance

```solidity
function _increaseBalance(address recipient, uint256 amount) internal
```

## IReceiveBalanceApproval

`IReceiveBalanceApproval` is an interface for a smart contract
consuming Bank balances approved to them in the same transaction by
other contracts or externally owned accounts (EOA).

### receiveBalanceApproval

```solidity
function receiveBalanceApproval(address owner, uint256 amount, bytes extraData) external
```

Called by the Bank in `approveBalanceAndCall` function after
the balance `owner` approved `amount` of their balance in the
Bank for the contract. This way, the depositor can approve
balance and call the contract to use the approved balance in
a single transaction.

The implementation must ensure this function can only be called
by the Bank. The Bank does _not_ guarantee that the `amount`
approved by the `owner` currently exists on their balance. That is,
the `owner` could approve more balance than they currently have.
This works the same as `Bank.approve` function. The contract must
ensure the actual balance is checked before performing any action
based on it.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| owner | address | Address of the Bank balance owner who approved their balance to be used by the contract. |
| amount | uint256 | The amount of the Bank balance approved by the owner to be used by the contract. |
| extraData | bytes | The `extraData` passed to `Bank.approveBalanceAndCall`. |

## BitcoinTx

Allows to reference Bitcoin raw transaction in Solidity.

See https://developer.bitcoin.org/reference/transactions.html#raw-transaction-format

Raw Bitcoin transaction data:

| Bytes  |     Name     |        BTC type        |        Description        |
|--------|--------------|------------------------|---------------------------|
| 4      | version      | int32_t (LE)           | TX version number         |
| varies | tx_in_count  | compactSize uint (LE)  | Number of TX inputs       |
| varies | tx_in        | txIn[]                 | TX inputs                 |
| varies | tx_out_count | compactSize uint (LE)  | Number of TX outputs      |
| varies | tx_out       | txOut[]                | TX outputs                |
| 4      | lock_time    | uint32_t (LE)          | Unix time or block number |

Non-coinbase transaction input (txIn):

| Bytes  |       Name       |        BTC type        |                 Description                 |
|--------|------------------|------------------------|---------------------------------------------|
| 36     | previous_output  | outpoint               | The previous outpoint being spent           |
| varies | script_bytes     | compactSize uint (LE)  | The number of bytes in the signature script |
| varies | signature_script | char[]                 | The signature script, empty for P2WSH       |
| 4      | sequence         | uint32_t (LE)          | Sequence number                             |

The reference to transaction being spent (outpoint):

| Bytes | Name  |   BTC type    |               Description                |
|-------|-------|---------------|------------------------------------------|
|    32 | hash  | char[32]      | Hash of the transaction to spend         |
|    4  | index | uint32_t (LE) | Index of the specific output from the TX |

Transaction output (txOut):

| Bytes  |      Name       |     BTC type          |             Description              |
|--------|-----------------|-----------------------|--------------------------------------|
| 8      | value           | int64_t (LE)          | Number of satoshis to spend          |
| 1+     | pk_script_bytes | compactSize uint (LE) | Number of bytes in the pubkey script |
| varies | pk_script       | char[]                | Pubkey script                        |

compactSize uint format:

|                  Value                  | Bytes |                    Format                    |
|-----------------------------------------|-------|----------------------------------------------|
| >= 0 && <= 252                          | 1     | uint8_t                                      |
| >= 253 && <= 0xffff                     | 3     | 0xfd followed by the number as uint16_t (LE) |
| >= 0x10000 && <= 0xffffffff             | 5     | 0xfe followed by the number as uint32_t (LE) |
| >= 0x100000000 && <= 0xffffffffffffffff | 9     | 0xff followed by the number as uint64_t (LE) |

(*) compactSize uint is often references as VarInt)

Coinbase transaction input (txIn):

| Bytes  |       Name       |        BTC type        |                 Description                 |
|--------|------------------|------------------------|---------------------------------------------|
| 32     | hash             | char[32]               | A 32-byte 0x0  null (no previous_outpoint)  |
| 4      | index            | uint32_t (LE)          | 0xffffffff (no previous_outpoint)           |
| varies | script_bytes     | compactSize uint (LE)  | The number of bytes in the coinbase script  |
| varies | height           | char[]                 | The block height of this block (BIP34) (*)  |
| varies | coinbase_script  | none                   |  Arbitrary data, max 100 bytes              |
| 4      | sequence         | uint32_t (LE)          | Sequence number

(*)  Uses script language: starts with a data-pushing opcode that indicates how many bytes to push to
the stack followed by the block height as a little-endian unsigned integer. This script must be as
short as possible, otherwise it may be rejected. The data-pushing opcode will be 0x03 and the total
size four bytes until block 16,777,216 about 300 years from now.

### Info

```solidity
struct Info {
  bytes4 version;
  bytes inputVector;
  bytes outputVector;
  bytes4 locktime;
}
```

### Proof

```solidity
struct Proof {
  bytes merkleProof;
  uint256 txIndexInBlock;
  bytes bitcoinHeaders;
}
```

### UTXO

```solidity
struct UTXO {
  bytes32 txHash;
  uint32 txOutputIndex;
  uint64 txOutputValue;
}
```

### RSVSignature

```solidity
struct RSVSignature {
  bytes32 r;
  bytes32 s;
  uint8 v;
}
```

### validateProof

```solidity
function validateProof(struct BridgeState.Storage self, struct BitcoinTx.Info txInfo, struct BitcoinTx.Proof proof) internal view returns (bytes32 txHash)
```

Validates the SPV proof of the Bitcoin transaction.
Reverts in case the validation or proof verification fail.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| txInfo | struct BitcoinTx.Info | Bitcoin transaction data. |
| proof | struct BitcoinTx.Proof | Bitcoin proof data. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| txHash | bytes32 | Proven 32-byte transaction hash. |

### evaluateProofDifficulty

```solidity
function evaluateProofDifficulty(struct BridgeState.Storage self, bytes bitcoinHeaders) internal view
```

Evaluates the given Bitcoin proof difficulty against the actual
Bitcoin chain difficulty provided by the relay oracle.
Reverts in case the evaluation fails.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| bitcoinHeaders | bytes | Bitcoin headers chain being part of the SPV proof. Used to extract the observed proof difficulty. |

### extractPubKeyHash

```solidity
function extractPubKeyHash(struct BridgeState.Storage, bytes output) internal pure returns (bytes20 pubKeyHash)
```

Extracts public key hash from the provided P2PKH or P2WPKH output.
Reverts if the validation fails.

Requirements:
- The output must be of P2PKH or P2WPKH type and lock the funds
on a 20-byte public key hash.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
|  | struct BridgeState.Storage |  |
| output | bytes | The transaction output. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| pubKeyHash | bytes20 | 20-byte public key hash the output locks funds on. |

### makeP2PKHScript

```solidity
function makeP2PKHScript(bytes20 pubKeyHash) internal pure returns (bytes26)
```

Build the P2PKH script from the given public key hash.

The P2PKH script has the following byte format:
<0x1976a914> <20-byte PKH> <0x88ac>. According to
https://en.bitcoin.it/wiki/Script#Opcodes this translates to:
- 0x19: Byte length of the entire script
- 0x76: OP_DUP
- 0xa9: OP_HASH160
- 0x14: Byte length of the public key hash
- 0x88: OP_EQUALVERIFY
- 0xac: OP_CHECKSIG
which matches the P2PKH structure as per:
https://en.bitcoin.it/wiki/Transaction#Pay-to-PubkeyHash

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| pubKeyHash | bytes20 | The 20-byte public key hash. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bytes26 | The P2PKH script. |

### makeP2WPKHScript

```solidity
function makeP2WPKHScript(bytes20 pubKeyHash) internal pure returns (bytes23)
```

Build the P2WPKH script from the given public key hash.

The P2WPKH script has the following format:
<0x160014> <20-byte PKH>. According to
https://en.bitcoin.it/wiki/Script#Opcodes this translates to:
- 0x16: Byte length of the entire script
- 0x00: OP_0
- 0x14: Byte length of the public key hash
which matches the P2WPKH structure as per:
https://github.com/bitcoin/bips/blob/master/bip-0141.mediawiki#P2WPKH

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| pubKeyHash | bytes20 | The 20-byte public key hash. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bytes23 | The P2WPKH script. |

## Bridge

Bridge manages BTC deposit and redemption flow and is increasing and
decreasing balances in the Bank as a result of BTC deposit and
redemption operations performed by depositors and redeemers.

Depositors send BTC funds to the most recently created off-chain
ECDSA wallet of the bridge using pay-to-script-hash (P2SH) or
pay-to-witness-script-hash (P2WSH) containing hashed information
about the depositor’s Ethereum address. Then, the depositor reveals
their Ethereum address along with their deposit blinding factor,
refund public key hash and refund locktime to the Bridge on Ethereum
chain. The off-chain ECDSA wallet listens for these sorts of
messages and when it gets one, it checks the Bitcoin network to make
sure the deposit lines up. If it does, the off-chain ECDSA wallet
may decide to pick the deposit transaction for sweeping, and when
the sweep operation is confirmed on the Bitcoin network, the ECDSA
wallet informs the Bridge about the sweep increasing appropriate
balances in the Bank.

Bridge is an upgradeable component of the Bank. The order of
functionalities in this contract is: deposit, sweep, redemption,
moving funds, wallet lifecycle, frauds, parameters.

### self

```solidity
struct BridgeState.Storage self
```

### DepositRevealed

```solidity
event DepositRevealed(bytes32 fundingTxHash, uint32 fundingOutputIndex, address depositor, uint64 amount, bytes8 blindingFactor, bytes20 walletPubKeyHash, bytes20 refundPubKeyHash, bytes4 refundLocktime, address vault)
```

### DepositsSwept

```solidity
event DepositsSwept(bytes20 walletPubKeyHash, bytes32 sweepTxHash)
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

### WalletMovingFunds

```solidity
event WalletMovingFunds(bytes32 ecdsaWalletID, bytes20 walletPubKeyHash)
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

### NewWalletRequested

```solidity
event NewWalletRequested()
```

### NewWalletRegistered

```solidity
event NewWalletRegistered(bytes32 ecdsaWalletID, bytes20 walletPubKeyHash)
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

### FraudChallengeSubmitted

```solidity
event FraudChallengeSubmitted(bytes20 walletPubKeyHash, bytes32 sighash, uint8 v, bytes32 r, bytes32 s)
```

### FraudChallengeDefeated

```solidity
event FraudChallengeDefeated(bytes20 walletPubKeyHash, bytes32 sighash)
```

### FraudChallengeDefeatTimedOut

```solidity
event FraudChallengeDefeatTimedOut(bytes20 walletPubKeyHash, bytes32 sighash)
```

### VaultStatusUpdated

```solidity
event VaultStatusUpdated(address vault, bool isTrusted)
```

### SpvMaintainerStatusUpdated

```solidity
event SpvMaintainerStatusUpdated(address spvMaintainer, bool isTrusted)
```

### DepositParametersUpdated

```solidity
event DepositParametersUpdated(uint64 depositDustThreshold, uint64 depositTreasuryFeeDivisor, uint64 depositTxMaxFee, uint32 depositRevealAheadPeriod)
```

### RedemptionParametersUpdated

```solidity
event RedemptionParametersUpdated(uint64 redemptionDustThreshold, uint64 redemptionTreasuryFeeDivisor, uint64 redemptionTxMaxFee, uint64 redemptionTxMaxTotalFee, uint32 redemptionTimeout, uint96 redemptionTimeoutSlashingAmount, uint32 redemptionTimeoutNotifierRewardMultiplier)
```

### MovingFundsParametersUpdated

```solidity
event MovingFundsParametersUpdated(uint64 movingFundsTxMaxTotalFee, uint64 movingFundsDustThreshold, uint32 movingFundsTimeoutResetDelay, uint32 movingFundsTimeout, uint96 movingFundsTimeoutSlashingAmount, uint32 movingFundsTimeoutNotifierRewardMultiplier, uint16 movingFundsCommitmentGasOffset, uint64 movedFundsSweepTxMaxTotalFee, uint32 movedFundsSweepTimeout, uint96 movedFundsSweepTimeoutSlashingAmount, uint32 movedFundsSweepTimeoutNotifierRewardMultiplier)
```

### WalletParametersUpdated

```solidity
event WalletParametersUpdated(uint32 walletCreationPeriod, uint64 walletCreationMinBtcBalance, uint64 walletCreationMaxBtcBalance, uint64 walletClosureMinBtcBalance, uint32 walletMaxAge, uint64 walletMaxBtcTransfer, uint32 walletClosingPeriod)
```

### FraudParametersUpdated

```solidity
event FraudParametersUpdated(uint96 fraudChallengeDepositAmount, uint32 fraudChallengeDefeatTimeout, uint96 fraudSlashingAmount, uint32 fraudNotifierRewardMultiplier)
```

### TreasuryUpdated

```solidity
event TreasuryUpdated(address treasury)
```

### onlySpvMaintainer

```solidity
modifier onlySpvMaintainer()
```

### constructor

```solidity
constructor() public
```

### initialize

```solidity
function initialize(address _bank, address _relay, address _treasury, address _ecdsaWalletRegistry, address payable _reimbursementPool, uint96 _txProofDifficultyFactor) external
```

Initializes upgradable contract on deployment.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _bank | address | Address of the Bank the Bridge belongs to. |
| _relay | address | Address of the Bitcoin relay providing the current Bitcoin network difficulty. |
| _treasury | address | Address where the deposit and redemption treasury fees will be sent to. |
| _ecdsaWalletRegistry | address | Address of the ECDSA Wallet Registry contract. |
| _reimbursementPool | address payable | Address of the Reimbursement Pool contract. |
| _txProofDifficultyFactor | uint96 | The number of confirmations on the Bitcoin chain required to successfully evaluate an SPV proof. |

### revealDeposit

```solidity
function revealDeposit(struct BitcoinTx.Info fundingTx, struct Deposit.DepositRevealInfo reveal) external
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
| fundingTx | struct BitcoinTx.Info | Bitcoin funding transaction data, see `BitcoinTx.Info`. |
| reveal | struct Deposit.DepositRevealInfo | Deposit reveal data, see `RevealInfo struct. |

### submitDepositSweepProof

```solidity
function submitDepositSweepProof(struct BitcoinTx.Info sweepTx, struct BitcoinTx.Proof sweepProof, struct BitcoinTx.UTXO mainUtxo, address vault) external
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
| sweepTx | struct BitcoinTx.Info | Bitcoin sweep transaction data. |
| sweepProof | struct BitcoinTx.Proof | Bitcoin sweep proof data. |
| mainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. If no main UTXO exists for the given wallet, this parameter is ignored. |
| vault | address | Optional address of the vault where all swept deposits should be routed to. All deposits swept as part of the transaction must have their `vault` parameters set to the same address. If this parameter is set to an address of a trusted vault, swept deposits are routed to that vault. If this parameter is set to the zero address or to an address of a non-trusted vault, swept deposits are not routed to a vault but depositors' balances are increased in the Bank individually. |

### requestRedemption

```solidity
function requestRedemption(bytes20 walletPubKeyHash, struct BitcoinTx.UTXO mainUtxo, bytes redeemerOutputScript, uint64 amount) external
```

Requests redemption of the given amount from the specified
wallet to the redeemer Bitcoin output script. Handles the
simplest case in which the redeemer's balance is decreased in
the Bank.

Requirements:
- Wallet behind `walletPubKeyHash` must be live,
- `mainUtxo` components must point to the recent main UTXO
of the given wallet, as currently known on the Ethereum chain,
- `redeemerOutputScript` must be a proper Bitcoin script,
- `redeemerOutputScript` cannot have wallet PKH as payload,
- `amount` must be above or equal the `redemptionDustThreshold`,
- Given `walletPubKeyHash` and `redeemerOutputScript` pair can be
used for only one pending request at the same time,
- Wallet must have enough Bitcoin balance to process the request,
- Redeemer must make an allowance in the Bank that the Bridge
contract can spend the given `amount`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletPubKeyHash | bytes20 | The 20-byte wallet public key hash (computed using Bitcoin HASH160 over the compressed ECDSA public key). |
| mainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. |
| redeemerOutputScript | bytes | The redeemer's length-prefixed output script (P2PKH, P2WPKH, P2SH or P2WSH) that will be used to lock redeemed BTC. |
| amount | uint64 | Requested amount in satoshi. This is also the Bank balance that is taken from the `balanceOwner` upon request. Once the request is handled, the actual amount of BTC locked on the redeemer output script will be always lower than this value since the treasury and Bitcoin transaction fees must be incurred. The minimal amount satisfying the request can be computed as: `amount - (amount / redemptionTreasuryFeeDivisor) - redemptionTxMaxFee`. Fees values are taken at the moment of request creation. |

### receiveBalanceApproval

```solidity
function receiveBalanceApproval(address balanceOwner, uint256 amount, bytes redemptionData) external
```

Requests redemption of the given amount from the specified
wallet to the redeemer Bitcoin output script. Used by
`Bank.approveBalanceAndCall`. Can handle more complex cases
where balance owner may be someone else than the redeemer.
For example, vault redeeming its balance for some depositor.

Requirements:
- The caller must be the Bank,
- Wallet behind `walletPubKeyHash` must be live,
- `mainUtxo` components must point to the recent main UTXO
of the given wallet, as currently known on the Ethereum chain,
- `redeemerOutputScript` must be a proper Bitcoin script,
- `redeemerOutputScript` cannot have wallet PKH as payload,
- `amount` must be above or equal the `redemptionDustThreshold`,
- Given `walletPubKeyHash` and `redeemerOutputScript` pair can be
used for only one pending request at the same time,
- Wallet must have enough Bitcoin balance to process the request.

Note on upgradeability:
Bridge is an upgradeable contract deployed behind
a TransparentUpgradeableProxy. Accepting redemption data as bytes
provides great flexibility. The Bridge is just like any other
contract with a balance approved in the Bank and can be upgraded
to another version without being bound to a particular interface
forever. This flexibility comes with the cost - developers
integrating their vaults and dApps with `Bridge` using
`approveBalanceAndCall` need to pay extra attention to
`redemptionData` and adjust the code in case the expected structure
of `redemptionData`  changes.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| balanceOwner | address | The address of the Bank balance owner whose balance is getting redeemed. |
| amount | uint256 | Requested amount in satoshi. This is also the Bank balance that is taken from the `balanceOwner` upon request. Once the request is handled, the actual amount of BTC locked on the redeemer output script will be always lower than this value since the treasury and Bitcoin transaction fees must be incurred. The minimal amount satisfying the request can be computed as: `amount - (amount / redemptionTreasuryFeeDivisor) - redemptionTxMaxFee`. Fees values are taken at the moment of request creation. |
| redemptionData | bytes | ABI-encoded redemption data: [ address redeemer, bytes20 walletPubKeyHash, bytes32 mainUtxoTxHash, uint32 mainUtxoTxOutputIndex, uint64 mainUtxoTxOutputValue, bytes redeemerOutputScript ] - redeemer: The Ethereum address of the redeemer who will be able to claim Bank balance if anything goes wrong during the redemption. In the most basic case, when someone redeems their balance from the Bank, `balanceOwner` is the same as `redeemer`. However, when a Vault is redeeming part of its balance for some redeemer address (for example, someone who has earlier deposited into that Vault), `balanceOwner` is the Vault, and `redeemer` is the address for which the vault is redeeming its balance to, - walletPubKeyHash: The 20-byte wallet public key hash (computed using Bitcoin HASH160 over the compressed ECDSA public key), - mainUtxoTxHash: Data of the wallet's main UTXO TX hash, as currently known on the Ethereum chain, - mainUtxoTxOutputIndex: Data of the wallet's main UTXO output index, as currently known on Ethereum chain, - mainUtxoTxOutputValue: Data of the wallet's main UTXO output value, as currently known on Ethereum chain, - redeemerOutputScript The redeemer's length-prefixed output script (P2PKH, P2WPKH, P2SH or P2WSH) that will be used to lock redeemed BTC. |

### submitRedemptionProof

```solidity
function submitRedemptionProof(struct BitcoinTx.Info redemptionTx, struct BitcoinTx.Proof redemptionProof, struct BitcoinTx.UTXO mainUtxo, bytes20 walletPubKeyHash) external
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
| redemptionTx | struct BitcoinTx.Info | Bitcoin redemption transaction data. |
| redemptionProof | struct BitcoinTx.Proof | Bitcoin redemption proof data. |
| mainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. |
| walletPubKeyHash | bytes20 | 20-byte public key hash (computed using Bitcoin HASH160 over the compressed ECDSA public key) of the wallet which performed the redemption transaction. |

### notifyRedemptionTimeout

```solidity
function notifyRedemptionTimeout(bytes20 walletPubKeyHash, uint32[] walletMembersIDs, bytes redeemerOutputScript) external
```

Notifies that there is a pending redemption request associated
with the given wallet, that has timed out. The redemption
request is identified by the key built as
`keccak256(keccak256(redeemerOutputScript) | walletPubKeyHash)`.
The results of calling this function:
- The pending redemptions value for the wallet will be decreased
by the requested amount (minus treasury fee),
- The tokens taken from the redeemer on redemption request will
be returned to the redeemer,
- The request will be moved from pending redemptions to
timed-out redemptions,
- If the state of the wallet is `Live` or `MovingFunds`, the
wallet operators will be slashed and the notifier will be
rewarded,
- If the state of wallet is `Live`, the wallet will be closed or
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
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members. |
| redeemerOutputScript | bytes | The redeemer's length-prefixed output script (P2PKH, P2WPKH, P2SH or P2WSH). |

### submitMovingFundsCommitment

```solidity
function submitMovingFundsCommitment(bytes20 walletPubKeyHash, struct BitcoinTx.UTXO walletMainUtxo, uint32[] walletMembersIDs, uint256 walletMemberIndex, bytes20[] targetWallets) external
```

Submits the moving funds target wallets commitment.
Once all requirements are met, that function registers the
target wallets commitment and opens the way for moving funds
proof submission.
The caller is reimbursed for the transaction costs.

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
| walletPubKeyHash | bytes20 | 20-byte public key hash of the source wallet. |
| walletMainUtxo | struct BitcoinTx.UTXO | Data of the source wallet's main UTXO, as currently known on the Ethereum chain. |
| walletMembersIDs | uint32[] | Identifiers of the source wallet signing group members. |
| walletMemberIndex | uint256 | Position of the caller in the source wallet signing group members list. |
| targetWallets | bytes20[] | List of 20-byte public key hashes of the target wallets that the source wallet commits to move the funds to. |

### resetMovingFundsTimeout

```solidity
function resetMovingFundsTimeout(bytes20 walletPubKeyHash) external
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
| walletPubKeyHash | bytes20 | 20-byte public key hash of the moving funds wallet. |

### submitMovingFundsProof

```solidity
function submitMovingFundsProof(struct BitcoinTx.Info movingFundsTx, struct BitcoinTx.Proof movingFundsProof, struct BitcoinTx.UTXO mainUtxo, bytes20 walletPubKeyHash) external
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
| movingFundsTx | struct BitcoinTx.Info | Bitcoin moving funds transaction data. |
| movingFundsProof | struct BitcoinTx.Proof | Bitcoin moving funds proof data. |
| mainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. |
| walletPubKeyHash | bytes20 | 20-byte public key hash (computed using Bitcoin HASH160 over the compressed ECDSA public key) of the wallet which performed the moving funds transaction. |

### notifyMovingFundsTimeout

```solidity
function notifyMovingFundsTimeout(bytes20 walletPubKeyHash, uint32[] walletMembersIDs) external
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
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members. |

### notifyMovingFundsBelowDust

```solidity
function notifyMovingFundsBelowDust(bytes20 walletPubKeyHash, struct BitcoinTx.UTXO mainUtxo) external
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
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet |
| mainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. |

### submitMovedFundsSweepProof

```solidity
function submitMovedFundsSweepProof(struct BitcoinTx.Info sweepTx, struct BitcoinTx.Proof sweepProof, struct BitcoinTx.UTXO mainUtxo) external
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
the first input pointing to a moved funds sweep request targeted
to the wallet, and optionally, the second input pointing to the
wallet's main UTXO, if the sweeping wallet has a main UTXO set.
There should be only one output locking funds on the sweeping
wallet 20-byte public key hash,
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
| sweepTx | struct BitcoinTx.Info | Bitcoin sweep funds transaction data. |
| sweepProof | struct BitcoinTx.Proof | Bitcoin sweep funds proof data. |
| mainUtxo | struct BitcoinTx.UTXO | Data of the sweeping wallet's main UTXO, as currently known on the Ethereum chain. |

### notifyMovedFundsSweepTimeout

```solidity
function notifyMovedFundsSweepTimeout(bytes32 movingFundsTxHash, uint32 movingFundsTxOutputIndex, uint32[] walletMembersIDs) external
```

Notifies about a timed out moved funds sweep process. If the
wallet is not terminated yet, that function terminates
the wallet and slashes signing group members as a result.
Marks the given sweep request as TimedOut.

Requirements:
- The moved funds sweep request must be in the Pending state,
- The moved funds sweep timeout must be actually exceeded,
- The wallet must be either in the Live or MovingFunds or
Terminated state,
- The expression `keccak256(abi.encode(walletMembersIDs))` must
be exactly the same as the hash stored under `membersIdsHash`
for the given `walletID`. Those IDs are not directly stored
in the contract for gas efficiency purposes but they can be
read from appropriate `DkgResultSubmitted` and `DkgResultApproved`
events of the `WalletRegistry` contract.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| movingFundsTxHash | bytes32 | 32-byte hash of the moving funds transaction that caused the sweep request to be created. |
| movingFundsTxOutputIndex | uint32 | Index of the moving funds transaction output that is subject of the sweep request. |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members. |

### requestNewWallet

```solidity
function requestNewWallet(struct BitcoinTx.UTXO activeWalletMainUtxo) external
```

Requests creation of a new wallet. This function just
forms a request and the creation process is performed
asynchronously. Once a wallet is created, the ECDSA Wallet
Registry will notify this contract by calling the
`__ecdsaWalletCreatedCallback` function.

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
| activeWalletMainUtxo | struct BitcoinTx.UTXO | Data of the active wallet's main UTXO, as currently known on the Ethereum chain. |

### __ecdsaWalletCreatedCallback

```solidity
function __ecdsaWalletCreatedCallback(bytes32 ecdsaWalletID, bytes32 publicKeyX, bytes32 publicKeyY) external
```

A callback function that is called by the ECDSA Wallet Registry
once a new ECDSA wallet is created.

Requirements:
- The only caller authorized to call this function is `registry`,
- Given wallet data must not belong to an already registered wallet.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| ecdsaWalletID | bytes32 | Wallet's unique identifier. |
| publicKeyX | bytes32 | Wallet's public key's X coordinate. |
| publicKeyY | bytes32 | Wallet's public key's Y coordinate. |

### __ecdsaWalletHeartbeatFailedCallback

```solidity
function __ecdsaWalletHeartbeatFailedCallback(bytes32, bytes32 publicKeyX, bytes32 publicKeyY) external
```

A callback function that is called by the ECDSA Wallet Registry
once a wallet heartbeat failure is detected.

Requirements:
- The only caller authorized to call this function is `registry`,
- Wallet must be in Live state.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
|  | bytes32 |  |
| publicKeyX | bytes32 | Wallet's public key's X coordinate. |
| publicKeyY | bytes32 | Wallet's public key's Y coordinate. |

### notifyWalletCloseable

```solidity
function notifyWalletCloseable(bytes20 walletPubKeyHash, struct BitcoinTx.UTXO walletMainUtxo) external
```

Notifies that the wallet is either old enough or has too few
satoshi left and qualifies to be closed.

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
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |
| walletMainUtxo | struct BitcoinTx.UTXO | Data of the wallet's main UTXO, as currently known on the Ethereum chain. |

### notifyWalletClosingPeriodElapsed

```solidity
function notifyWalletClosingPeriodElapsed(bytes20 walletPubKeyHash) external
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
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet. |

### submitFraudChallenge

```solidity
function submitFraudChallenge(bytes walletPublicKey, bytes preimageSha256, struct BitcoinTx.RSVSignature signature) external payable
```

Submits a fraud challenge indicating that a UTXO being under
wallet control was unlocked by the wallet but was not used
according to the protocol rules. That means the wallet signed
a transaction input pointing to that UTXO and there is a unique
sighash and signature pair associated with that input. This
function uses those parameters to create a fraud accusation that
proves a given transaction input unlocking the given UTXO was
actually signed by the wallet. This function cannot determine
whether the transaction was actually broadcast and the input was
consumed in a fraudulent way so it just opens a challenge period
during which the wallet can defeat the challenge by submitting
proof of a transaction that consumes the given input according
to protocol rules. To prevent spurious allegations, the caller
must deposit ETH that is returned back upon justified fraud
challenge or confiscated otherwise.

Requirements:
- Wallet behind `walletPublicKey` must be in Live or MovingFunds
or Closing state,
- The challenger must send appropriate amount of ETH used as
fraud challenge deposit,
- The signature (represented by r, s and v) must be generated by
the wallet behind `walletPubKey` during signing of `sighash`
which was calculated from `preimageSha256`,
- Wallet can be challenged for the given signature only once.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletPublicKey | bytes | The public key of the wallet in the uncompressed and unprefixed format (64 bytes). |
| preimageSha256 | bytes | The hash that was generated by applying SHA-256 one time over the preimage used during input signing. The preimage is a serialized subset of the transaction and its structure depends on the transaction input (see BIP-143 for reference). Notice that applying SHA-256 over the `preimageSha256` results in `sighash`.  The path from `preimage` to `sighash` looks like this: preimage -> (SHA-256) -> preimageSha256 -> (SHA-256) -> sighash. |
| signature | struct BitcoinTx.RSVSignature | Bitcoin signature in the R/S/V format. |

### defeatFraudChallenge

```solidity
function defeatFraudChallenge(bytes walletPublicKey, bytes preimage, bool witness) external
```

Allows to defeat a pending fraud challenge against a wallet if
the transaction that spends the UTXO follows the protocol rules.
In order to defeat the challenge the same `walletPublicKey` and
signature (represented by `r`, `s` and `v`) must be provided as
were used to calculate the sighash during input signing.
The fraud challenge defeat attempt will only succeed if the
inputs in the preimage are considered honestly spent by the
wallet. Therefore the transaction spending the UTXO must be
proven in the Bridge before a challenge defeat is called.
If successfully defeated, the fraud challenge is marked as
resolved and the amount of ether deposited by the challenger is
sent to the treasury.

Requirements:
- `walletPublicKey` and `sighash` calculated as `hash256(preimage)`
must identify an open fraud challenge,
- the preimage must be a valid preimage of a transaction generated
according to the protocol rules and already proved in the Bridge,
- before a defeat attempt is made the transaction that spends the
given UTXO must be proven in the Bridge.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletPublicKey | bytes | The public key of the wallet in the uncompressed and unprefixed format (64 bytes). |
| preimage | bytes | The preimage which produces sighash used to generate the ECDSA signature that is the subject of the fraud claim. It is a serialized subset of the transaction. The exact subset used as the preimage depends on the transaction input the signature is produced for. See BIP-143 for reference. |
| witness | bool | Flag indicating whether the preimage was produced for a witness input. True for witness, false for non-witness input. |

### defeatFraudChallengeWithHeartbeat

```solidity
function defeatFraudChallengeWithHeartbeat(bytes walletPublicKey, bytes heartbeatMessage) external
```

Allows to defeat a pending fraud challenge against a wallet by
proving the sighash and signature were produced for an off-chain
wallet heartbeat message following a strict format.
In order to defeat the challenge the same `walletPublicKey` and
signature (represented by `r`, `s` and `v`) must be provided as
were used to calculate the sighash during heartbeat message
signing. The fraud challenge defeat attempt will only succeed if
the signed message follows a strict format required for
heartbeat messages. If successfully defeated, the fraud
challenge is marked as resolved and the amount of ether
deposited by the challenger is sent to the treasury.

Requirements:
- `walletPublicKey` and `sighash` calculated as
`hash256(heartbeatMessage)` must identify an open fraud challenge,
- `heartbeatMessage` must follow a strict format of heartbeat
messages.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletPublicKey | bytes | The public key of the wallet in the uncompressed and unprefixed format (64 bytes). |
| heartbeatMessage | bytes | Off-chain heartbeat message meeting the heartbeat message format requirements which produces sighash used to generate the ECDSA signature that is the subject of the fraud claim. |

### notifyFraudChallengeDefeatTimeout

```solidity
function notifyFraudChallengeDefeatTimeout(bytes walletPublicKey, uint32[] walletMembersIDs, bytes preimageSha256) external
```

Notifies about defeat timeout for the given fraud challenge.
Can be called only if there was a fraud challenge identified by
the provided `walletPublicKey` and `sighash` and it was not
defeated on time. The amount of time that needs to pass after
a fraud challenge is reported is indicated by the
`challengeDefeatTimeout`. After a successful fraud challenge
defeat timeout notification the fraud challenge is marked as
resolved, the stake of each operator is slashed, the ether
deposited is returned to the challenger and the challenger is
rewarded.

Requirements:
- The wallet must be in the Live or MovingFunds or Closing or
Terminated state,
- The `walletPublicKey` and `sighash` calculated from
`preimageSha256` must identify an open fraud challenge,
- The expression `keccak256(abi.encode(walletMembersIDs))` must
be exactly the same as the hash stored under `membersIdsHash`
for the given `walletID`. Those IDs are not directly stored
in the contract for gas efficiency purposes but they can be
read from appropriate `DkgResultSubmitted` and `DkgResultApproved`
events of the `WalletRegistry` contract,
- The amount of time indicated by `challengeDefeatTimeout` must pass
after the challenge was reported.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletPublicKey | bytes | The public key of the wallet in the uncompressed and unprefixed format (64 bytes). |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members. |
| preimageSha256 | bytes | The hash that was generated by applying SHA-256 one time over the preimage used during input signing. The preimage is a serialized subset of the transaction and its structure depends on the transaction input (see BIP-143 for reference). Notice that applying SHA-256 over the `preimageSha256` results in `sighash`.  The path from `preimage` to `sighash` looks like this: preimage -> (SHA-256) -> preimageSha256 -> (SHA-256) -> sighash. |

### setVaultStatus

```solidity
function setVaultStatus(address vault, bool isTrusted) external
```

Allows the Governance to mark the given vault address as trusted
or no longer trusted. Vaults are not trusted by default.
Trusted vault must meet the following criteria:
- `IVault.receiveBalanceIncrease` must have a known, low gas
cost,
- `IVault.receiveBalanceIncrease` must never revert.

Without restricting reveal only to trusted vaults, malicious
vaults not meeting the criteria would be able to nuke sweep proof
transactions executed by ECDSA wallet with  deposits routed to
them.
Can only be called by the Governance.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| vault | address | The address of the vault. |
| isTrusted | bool | flag indicating whether the vault is trusted or not. |

### setSpvMaintainerStatus

```solidity
function setSpvMaintainerStatus(address spvMaintainer, bool isTrusted) external
```

Allows the Governance to mark the given address as trusted
or no longer trusted SPV maintainer. Addresses are not trusted
as SPV maintainers by default.

The SPV proof does not check whether the transaction is a part of
the Bitcoin mainnet, it only checks whether the transaction has been
mined performing the required amount of work as on Bitcoin mainnet.
The possibility of submitting SPV proofs is limited to trusted SPV
maintainers. The system expects transaction confirmations with the
required work accumulated, so trusted SPV maintainers can not prove
the transaction without providing the required Bitcoin proof of work.
Trusted maintainers address the issue of an economic game between
tBTC and Bitcoin mainnet where large Bitcoin mining pools can decide
to use their hash power to mine fake Bitcoin blocks to prove them in
tBTC instead of receiving Bitcoin miner rewards.
Can only be called by the Governance.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| spvMaintainer | address | The address of the SPV maintainer. |
| isTrusted | bool | flag indicating whether the address is trusted or not. |

### updateDepositParameters

```solidity
function updateDepositParameters(uint64 depositDustThreshold, uint64 depositTreasuryFeeDivisor, uint64 depositTxMaxFee, uint32 depositRevealAheadPeriod) external
```

of `depositTreasuryFeeDivisor` and `depositTxMaxFee` parameters
in order to make requests that can incur the treasury and
transaction fee and still satisfy the depositor.

Requirements:
- Deposit dust threshold must be greater than zero,
- Deposit dust threshold must be greater than deposit TX max fee,
- Deposit transaction max fee must be greater than zero.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| depositDustThreshold | uint64 |  |
| depositTreasuryFeeDivisor | uint64 | New value of the treasury fee divisor. It is the divisor used to compute the treasury fee taken from each deposit and transferred to the treasury upon sweep proof submission. That fee is computed as follows: `treasuryFee = depositedAmount / depositTreasuryFeeDivisor` For example, if the treasury fee needs to be 2% of each deposit, the `depositTreasuryFeeDivisor` should be set to `50` because `1/50 = 0.02 = 2%`. |
| depositTxMaxFee | uint64 | New value of the deposit tx max fee in satoshis. It is the maximum amount of BTC transaction fee that can be incurred by each swept deposit being part of the given sweep transaction. If the maximum BTC transaction fee is exceeded, such transaction is considered a fraud. |
| depositRevealAheadPeriod | uint32 | New value of the deposit reveal ahead period parameter in seconds. It defines the length of the period that must be preserved between the deposit reveal time and the deposit refund locktime. |

### updateRedemptionParameters

```solidity
function updateRedemptionParameters(uint64 redemptionDustThreshold, uint64 redemptionTreasuryFeeDivisor, uint64 redemptionTxMaxFee, uint64 redemptionTxMaxTotalFee, uint32 redemptionTimeout, uint96 redemptionTimeoutSlashingAmount, uint32 redemptionTimeoutNotifierRewardMultiplier) external
```

Updates parameters of redemptions.

Requirements:
- Redemption dust threshold must be greater than moving funds dust
threshold,
- Redemption dust threshold must be greater than the redemption TX
max fee,
- Redemption transaction max fee must be greater than zero,
- Redemption transaction max total fee must be greater than or
equal to the redemption transaction per-request max fee,
- Redemption timeout must be greater than zero,
- Redemption timeout notifier reward multiplier must be in the
range [0, 100].

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| redemptionDustThreshold | uint64 | New value of the redemption dust threshold in satoshis. It is the minimal amount that can be requested for redemption. Value of this parameter must take into account the value of `redemptionTreasuryFeeDivisor` and `redemptionTxMaxFee` parameters in order to make requests that can incur the treasury and transaction fee and still satisfy the redeemer. |
| redemptionTreasuryFeeDivisor | uint64 | New value of the redemption treasury fee divisor. It is the divisor used to compute the treasury fee taken from each redemption request and transferred to the treasury upon successful request finalization. That fee is computed as follows: `treasuryFee = requestedAmount / redemptionTreasuryFeeDivisor` For example, if the treasury fee needs to be 2% of each redemption request, the `redemptionTreasuryFeeDivisor` should be set to `50` because `1/50 = 0.02 = 2%`. |
| redemptionTxMaxFee | uint64 | New value of the redemption transaction max fee in satoshis. It is the maximum amount of BTC transaction fee that can be incurred by each redemption request being part of the given redemption transaction. If the maximum BTC transaction fee is exceeded, such transaction is considered a fraud. This is a per-redemption output max fee for the redemption transaction. |
| redemptionTxMaxTotalFee | uint64 | New value of the redemption transaction max total fee in satoshis. It is the maximum amount of the total BTC transaction fee that is acceptable in a single redemption transaction. This is a _total_ max fee for the entire redemption transaction. |
| redemptionTimeout | uint32 | New value of the redemption timeout in seconds. It is the time after which the redemption request can be reported as timed out. It is counted from the moment when the redemption request was created via `requestRedemption` call. Reported  timed out requests are cancelled and locked balance is returned to the redeemer in full amount. |
| redemptionTimeoutSlashingAmount | uint96 | New value of the redemption timeout slashing amount in T, it is the amount slashed from each wallet member for redemption timeout. |
| redemptionTimeoutNotifierRewardMultiplier | uint32 | New value of the redemption timeout notifier reward multiplier as percentage, it determines the percentage of the notifier reward from the staking contact the notifier of a redemption timeout receives. The value must be in the range [0, 100]. |

### updateMovingFundsParameters

```solidity
function updateMovingFundsParameters(uint64 movingFundsTxMaxTotalFee, uint64 movingFundsDustThreshold, uint32 movingFundsTimeoutResetDelay, uint32 movingFundsTimeout, uint96 movingFundsTimeoutSlashingAmount, uint32 movingFundsTimeoutNotifierRewardMultiplier, uint16 movingFundsCommitmentGasOffset, uint64 movedFundsSweepTxMaxTotalFee, uint32 movedFundsSweepTimeout, uint96 movedFundsSweepTimeoutSlashingAmount, uint32 movedFundsSweepTimeoutNotifierRewardMultiplier) external
```

Updates parameters of moving funds.

Requirements:
- Moving funds transaction max total fee must be greater than zero,
- Moving funds dust threshold must be greater than zero and lower
than the redemption dust threshold,
- Moving funds timeout reset delay must be greater than zero,
- Moving funds timeout must be greater than the moving funds
timeout reset delay,
- Moving funds timeout notifier reward multiplier must be in the
range [0, 100],
- Moved funds sweep transaction max total fee must be greater than zero,
- Moved funds sweep timeout must be greater than zero,
- Moved funds sweep timeout notifier reward multiplier must be in the
range [0, 100].

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| movingFundsTxMaxTotalFee | uint64 | New value of the moving funds transaction max total fee in satoshis. It is the maximum amount of the total BTC transaction fee that is acceptable in a single moving funds transaction. This is a _total_ max fee for the entire moving funds transaction. |
| movingFundsDustThreshold | uint64 | New value of the moving funds dust threshold. It is the minimal satoshi amount that makes sense to be transferred during the moving funds process. Moving funds wallets having their BTC balance below that value can begin closing immediately as transferring such a low value may not be possible due to BTC network fees. |
| movingFundsTimeoutResetDelay | uint32 | New value of the moving funds timeout reset delay in seconds. It is the time after which the moving funds timeout can be reset in case the target wallet commitment cannot be submitted due to a lack of live wallets in the system. It is counted from the moment when the wallet was requested to move their funds and switched to the MovingFunds state or from the moment the timeout was reset the last time. |
| movingFundsTimeout | uint32 | New value of the moving funds timeout in seconds. It is the time after which the moving funds process can be reported as timed out. It is counted from the moment when the wallet was requested to move their funds and switched to the MovingFunds state. |
| movingFundsTimeoutSlashingAmount | uint96 | New value of the moving funds timeout slashing amount in T, it is the amount slashed from each wallet member for moving funds timeout. |
| movingFundsTimeoutNotifierRewardMultiplier | uint32 | New value of the moving funds timeout notifier reward multiplier as percentage, it determines the percentage of the notifier reward from the staking contact the notifier of a moving funds timeout receives. The value must be in the range [0, 100]. |
| movingFundsCommitmentGasOffset | uint16 | New value of the gas offset for moving funds target wallet commitment transaction gas costs reimbursement. |
| movedFundsSweepTxMaxTotalFee | uint64 | New value of the moved funds sweep transaction max total fee in satoshis. It is the maximum amount of the total BTC transaction fee that is acceptable in a single moved funds sweep transaction. This is a _total_ max fee for the entire moved funds sweep transaction. |
| movedFundsSweepTimeout | uint32 | New value of the moved funds sweep timeout in seconds. It is the time after which the moved funds sweep process can be reported as timed out. It is counted from the moment when the wallet was requested to sweep the received funds. |
| movedFundsSweepTimeoutSlashingAmount | uint96 | New value of the moved funds sweep timeout slashing amount in T, it is the amount slashed from each wallet member for moved funds sweep timeout. |
| movedFundsSweepTimeoutNotifierRewardMultiplier | uint32 | New value of the moved funds sweep timeout notifier reward multiplier as percentage, it determines the percentage of the notifier reward from the staking contact the notifier of a moved funds sweep timeout receives. The value must be in the range [0, 100]. |

### updateWalletParameters

```solidity
function updateWalletParameters(uint32 walletCreationPeriod, uint64 walletCreationMinBtcBalance, uint64 walletCreationMaxBtcBalance, uint64 walletClosureMinBtcBalance, uint32 walletMaxAge, uint64 walletMaxBtcTransfer, uint32 walletClosingPeriod) external
```

Requirements:
- Wallet maximum BTC balance must be greater than the wallet
minimum BTC balance,
- Wallet maximum BTC transfer must be greater than zero,
- Wallet closing period must be greater than zero.

### updateFraudParameters

```solidity
function updateFraudParameters(uint96 fraudChallengeDepositAmount, uint32 fraudChallengeDefeatTimeout, uint96 fraudSlashingAmount, uint32 fraudNotifierRewardMultiplier) external
```

Updates parameters related to frauds.

Requirements:
- Fraud challenge defeat timeout must be greater than 0,
- Fraud notifier reward multiplier must be in the range [0, 100].

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| fraudChallengeDepositAmount | uint96 | New value of the fraud challenge deposit amount in wei, it is the amount of ETH the party challenging the wallet for fraud needs to deposit. |
| fraudChallengeDefeatTimeout | uint32 | New value of the challenge defeat timeout in seconds, it is the amount of time the wallet has to defeat a fraud challenge. The value must be greater than zero. |
| fraudSlashingAmount | uint96 | New value of the fraud slashing amount in T, it is the amount slashed from each wallet member for committing a fraud. |
| fraudNotifierRewardMultiplier | uint32 | New value of the fraud notifier reward multiplier as percentage, it determines the percentage of the notifier reward from the staking contact the notifier of a fraud receives. The value must be in the range [0, 100]. |

### updateTreasury

```solidity
function updateTreasury(address treasury) external
```

Updates treasury address. The treasury receives the system fees.

The treasury address must not be 0x0.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| treasury | address | New value of the treasury address. |

### deposits

```solidity
function deposits(uint256 depositKey) external view returns (struct Deposit.DepositRequest)
```

Collection of all revealed deposits indexed by
keccak256(fundingTxHash | fundingOutputIndex).
The fundingTxHash is bytes32 (ordered as in Bitcoin internally)
and fundingOutputIndex an uint32. This mapping may contain valid
and invalid deposits and the wallet is responsible for
validating them before attempting to execute a sweep.

### pendingRedemptions

```solidity
function pendingRedemptions(uint256 redemptionKey) external view returns (struct Redemption.RedemptionRequest)
```

Collection of all pending redemption requests indexed by
redemption key built as
`keccak256(keccak256(redeemerOutputScript) | walletPubKeyHash)`.
The walletPubKeyHash is the 20-byte wallet's public key hash
(computed using Bitcoin HASH160 over the compressed ECDSA
public key) and `redeemerOutputScript` is a Bitcoin script
(P2PKH, P2WPKH, P2SH or P2WSH) that will be used to lock
redeemed BTC as requested by the redeemer. Requests are added
to this mapping by the `requestRedemption` method (duplicates
not allowed) and are removed by one of the following methods:
- `submitRedemptionProof` in case the request was handled
successfully,
- `notifyRedemptionTimeout` in case the request was reported
to be timed out.

### timedOutRedemptions

```solidity
function timedOutRedemptions(uint256 redemptionKey) external view returns (struct Redemption.RedemptionRequest)
```

Collection of all timed out redemptions requests indexed by
redemption key built as
`keccak256(keccak256(redeemerOutputScript) | walletPubKeyHash)`.
The walletPubKeyHash is the 20-byte wallet's public key hash
(computed using Bitcoin HASH160 over the compressed ECDSA
public key) and `redeemerOutputScript` is the Bitcoin script
(P2PKH, P2WPKH, P2SH or P2WSH) that is involved in the timed
out request.
Only one method can add to this mapping:
- `notifyRedemptionTimeout` which puts the redemption key
to this mapping based on a timed out request stored
previously in `pendingRedemptions` mapping.
Only one method can remove entries from this mapping:
- `submitRedemptionProof` in case the timed out redemption
request was a part of the proven transaction.

### spentMainUTXOs

```solidity
function spentMainUTXOs(uint256 utxoKey) external view returns (bool)
```

Collection of main UTXOs that are honestly spent indexed by
keccak256(fundingTxHash | fundingOutputIndex). The fundingTxHash
is bytes32 (ordered as in Bitcoin internally) and
fundingOutputIndex an uint32. A main UTXO is considered honestly
spent if it was used as an input of a transaction that have been
proven in the Bridge.

### wallets

```solidity
function wallets(bytes20 walletPubKeyHash) external view returns (struct Wallets.Wallet)
```

Gets details about a registered wallet.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletPubKeyHash | bytes20 | The 20-byte wallet public key hash (computed using Bitcoin HASH160 over the compressed ECDSA public key). |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | struct Wallets.Wallet | Wallet details. |

### activeWalletPubKeyHash

```solidity
function activeWalletPubKeyHash() external view returns (bytes20)
```

Gets the public key hash of the active wallet.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bytes20 | The 20-byte public key hash (computed using Bitcoin HASH160 over the compressed ECDSA public key) of the active wallet. Returns bytes20(0) if there is no active wallet at the moment. |

### liveWalletsCount

```solidity
function liveWalletsCount() external view returns (uint32)
```

Gets the live wallets count.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint32 | The current count of wallets being in the Live state. |

### fraudChallenges

```solidity
function fraudChallenges(uint256 challengeKey) external view returns (struct Fraud.FraudChallenge)
```

Returns the fraud challenge identified by the given key built
as keccak256(walletPublicKey|sighash).

### movedFundsSweepRequests

```solidity
function movedFundsSweepRequests(uint256 requestKey) external view returns (struct MovingFunds.MovedFundsSweepRequest)
```

Collection of all moved funds sweep requests indexed by
`keccak256(movingFundsTxHash | movingFundsOutputIndex)`.
The `movingFundsTxHash` is `bytes32` (ordered as in Bitcoin
internally) and `movingFundsOutputIndex` an `uint32`. Each entry
is actually an UTXO representing the moved funds and is supposed
to be swept with the current main UTXO of the recipient wallet.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| requestKey | uint256 | Request key built as `keccak256(movingFundsTxHash | movingFundsOutputIndex)`. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | struct MovingFunds.MovedFundsSweepRequest | Details of the moved funds sweep request. |

### isVaultTrusted

```solidity
function isVaultTrusted(address vault) external view returns (bool)
```

Indicates if the vault with the given address is trusted or not.
Depositors can route their revealed deposits only to trusted
vaults and have trusted vaults notified about new deposits as
soon as these deposits get swept. Vaults not trusted by the
Bridge can still be used by Bank balance owners on their own
responsibility - anyone can approve their Bank balance to any
address.

### depositParameters

```solidity
function depositParameters() external view returns (uint64 depositDustThreshold, uint64 depositTreasuryFeeDivisor, uint64 depositTxMaxFee, uint32 depositRevealAheadPeriod)
```

Returns the current values of Bridge deposit parameters.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| depositDustThreshold | uint64 | The minimal amount that can be requested to deposit. Value of this parameter must take into account the value of `depositTreasuryFeeDivisor` and `depositTxMaxFee` parameters in order to make requests that can incur the treasury and transaction fee and still satisfy the depositor. |
| depositTreasuryFeeDivisor | uint64 | Divisor used to compute the treasury fee taken from each deposit and transferred to the treasury upon sweep proof submission. That fee is computed as follows: `treasuryFee = depositedAmount / depositTreasuryFeeDivisor` For example, if the treasury fee needs to be 2% of each deposit, the `depositTreasuryFeeDivisor` should be set to `50` because `1/50 = 0.02 = 2%`. |
| depositTxMaxFee | uint64 | Maximum amount of BTC transaction fee that can be incurred by each swept deposit being part of the given sweep transaction. If the maximum BTC transaction fee is exceeded, such transaction is considered a fraud. |
| depositRevealAheadPeriod | uint32 | Defines the length of the period that must be preserved between the deposit reveal time and the deposit refund locktime. For example, if the deposit become refundable on August 1st, and the ahead period is 7 days, the latest moment for deposit reveal is July 25th. Value in seconds. |

### redemptionParameters

```solidity
function redemptionParameters() external view returns (uint64 redemptionDustThreshold, uint64 redemptionTreasuryFeeDivisor, uint64 redemptionTxMaxFee, uint64 redemptionTxMaxTotalFee, uint32 redemptionTimeout, uint96 redemptionTimeoutSlashingAmount, uint32 redemptionTimeoutNotifierRewardMultiplier)
```

Returns the current values of Bridge redemption parameters.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| redemptionDustThreshold | uint64 | The minimal amount that can be requested for redemption. Value of this parameter must take into account the value of `redemptionTreasuryFeeDivisor` and `redemptionTxMaxFee` parameters in order to make requests that can incur the treasury and transaction fee and still satisfy the redeemer. |
| redemptionTreasuryFeeDivisor | uint64 | Divisor used to compute the treasury fee taken from each redemption request and transferred to the treasury upon successful request finalization. That fee is computed as follows: `treasuryFee = requestedAmount / redemptionTreasuryFeeDivisor` For example, if the treasury fee needs to be 2% of each redemption request, the `redemptionTreasuryFeeDivisor` should be set to `50` because `1/50 = 0.02 = 2%`. |
| redemptionTxMaxFee | uint64 | Maximum amount of BTC transaction fee that can be incurred by each redemption request being part of the given redemption transaction. If the maximum BTC transaction fee is exceeded, such transaction is considered a fraud. This is a per-redemption output max fee for the redemption transaction. |
| redemptionTxMaxTotalFee | uint64 | Maximum amount of the total BTC transaction fee that is acceptable in a single redemption transaction. This is a _total_ max fee for the entire redemption transaction. |
| redemptionTimeout | uint32 | Time after which the redemption request can be reported as timed out. It is counted from the moment when the redemption request was created via `requestRedemption` call. Reported  timed out requests are cancelled and locked balance is returned to the redeemer in full amount. |
| redemptionTimeoutSlashingAmount | uint96 | The amount of stake slashed from each member of a wallet for a redemption timeout. |
| redemptionTimeoutNotifierRewardMultiplier | uint32 | The percentage of the notifier reward from the staking contract the notifier of a redemption timeout receives. The value is in the range [0, 100]. |

### movingFundsParameters

```solidity
function movingFundsParameters() external view returns (uint64 movingFundsTxMaxTotalFee, uint64 movingFundsDustThreshold, uint32 movingFundsTimeoutResetDelay, uint32 movingFundsTimeout, uint96 movingFundsTimeoutSlashingAmount, uint32 movingFundsTimeoutNotifierRewardMultiplier, uint16 movingFundsCommitmentGasOffset, uint64 movedFundsSweepTxMaxTotalFee, uint32 movedFundsSweepTimeout, uint96 movedFundsSweepTimeoutSlashingAmount, uint32 movedFundsSweepTimeoutNotifierRewardMultiplier)
```

Returns the current values of Bridge moving funds between
wallets parameters.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| movingFundsTxMaxTotalFee | uint64 | Maximum amount of the total BTC transaction fee that is acceptable in a single moving funds transaction. This is a _total_ max fee for the entire moving funds transaction. |
| movingFundsDustThreshold | uint64 | The minimal satoshi amount that makes sense to be transferred during the moving funds process. Moving funds wallets having their BTC balance below that value can begin closing immediately as transferring such a low value may not be possible due to BTC network fees. |
| movingFundsTimeoutResetDelay | uint32 | Time after which the moving funds timeout can be reset in case the target wallet commitment cannot be submitted due to a lack of live wallets in the system. It is counted from the moment when the wallet was requested to move their funds and switched to the MovingFunds state or from the moment the timeout was reset the last time. Value in seconds This value should be lower than the value of the `movingFundsTimeout`. |
| movingFundsTimeout | uint32 | Time after which the moving funds process can be reported as timed out. It is counted from the moment when the wallet was requested to move their funds and switched to the MovingFunds state. Value in seconds. |
| movingFundsTimeoutSlashingAmount | uint96 | The amount of stake slashed from each member of a wallet for a moving funds timeout. |
| movingFundsTimeoutNotifierRewardMultiplier | uint32 | The percentage of the notifier reward from the staking contract the notifier of a moving funds timeout receives. The value is in the range [0, 100]. |
| movingFundsCommitmentGasOffset | uint16 | The gas offset used for the moving funds target wallet commitment transaction cost reimbursement. |
| movedFundsSweepTxMaxTotalFee | uint64 | Maximum amount of the total BTC transaction fee that is acceptable in a single moved funds sweep transaction. This is a _total_ max fee for the entire moved funds sweep transaction. |
| movedFundsSweepTimeout | uint32 | Time after which the moved funds sweep process can be reported as timed out. It is counted from the moment when the wallet was requested to sweep the received funds. Value in seconds. |
| movedFundsSweepTimeoutSlashingAmount | uint96 | The amount of stake slashed from each member of a wallet for a moved funds sweep timeout. |
| movedFundsSweepTimeoutNotifierRewardMultiplier | uint32 | The percentage of the notifier reward from the staking contract the notifier of a moved funds sweep timeout receives. The value is in the range [0, 100]. |

### walletParameters

```solidity
function walletParameters() external view returns (uint32 walletCreationPeriod, uint64 walletCreationMinBtcBalance, uint64 walletCreationMaxBtcBalance, uint64 walletClosureMinBtcBalance, uint32 walletMaxAge, uint64 walletMaxBtcTransfer, uint32 walletClosingPeriod)
```

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletCreationPeriod | uint32 | Determines how frequently a new wallet creation can be requested. Value in seconds. |
| walletCreationMinBtcBalance | uint64 | The minimum BTC threshold in satoshi that is used to decide about wallet creation. |
| walletCreationMaxBtcBalance | uint64 | The maximum BTC threshold in satoshi that is used to decide about wallet creation. |
| walletClosureMinBtcBalance | uint64 | The minimum BTC threshold in satoshi that is used to decide about wallet closure. |
| walletMaxAge | uint32 | The maximum age of a wallet in seconds, after which the wallet moving funds process can be requested. |
| walletMaxBtcTransfer | uint64 | The maximum BTC amount in satoshi than can be transferred to a single target wallet during the moving funds process. |
| walletClosingPeriod | uint32 | Determines the length of the wallet closing period, i.e. the period when the wallet remains in the Closing state and can be subject of deposit fraud challenges. Value in seconds. |

### fraudParameters

```solidity
function fraudParameters() external view returns (uint96 fraudChallengeDepositAmount, uint32 fraudChallengeDefeatTimeout, uint96 fraudSlashingAmount, uint32 fraudNotifierRewardMultiplier)
```

Returns the current values of Bridge fraud parameters.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| fraudChallengeDepositAmount | uint96 | The amount of ETH in wei the party challenging the wallet for fraud needs to deposit. |
| fraudChallengeDefeatTimeout | uint32 | The amount of time the wallet has to defeat a fraud challenge. |
| fraudSlashingAmount | uint96 | The amount slashed from each wallet member for committing a fraud. |
| fraudNotifierRewardMultiplier | uint32 | The percentage of the notifier reward from the staking contract the notifier of a fraud receives. The value is in the range [0, 100]. |

### contractReferences

```solidity
function contractReferences() external view returns (contract Bank bank, contract IRelay relay, contract IWalletRegistry ecdsaWalletRegistry, contract ReimbursementPool reimbursementPool)
```

Returns the addresses of contracts Bridge is interacting with.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| bank | contract Bank | Address of the Bank the Bridge belongs to. |
| relay | contract IRelay | Address of the Bitcoin relay providing the current Bitcoin network difficulty. |
| ecdsaWalletRegistry | contract IWalletRegistry | Address of the ECDSA Wallet Registry. |
| reimbursementPool | contract ReimbursementPool | Address of the Reimbursement Pool. |

### treasury

```solidity
function treasury() external view returns (address)
```

Address where the deposit treasury fees will be sent to.
Treasury takes part in the operators rewarding process.

### txProofDifficultyFactor

```solidity
function txProofDifficultyFactor() external view returns (uint256)
```

The number of confirmations on the Bitcoin chain required to
successfully evaluate an SPV proof.

## BridgeGovernance

Owns the `Bridge` contract and is responsible for updating
its governable parameters in respect to governance delay individual
for each parameter. The other responsibility is marking a vault
address as trusted or no longer trusted.

### depositData

```solidity
struct BridgeGovernanceParameters.DepositData depositData
```

### redemptionData

```solidity
struct BridgeGovernanceParameters.RedemptionData redemptionData
```

### movingFundsData

```solidity
struct BridgeGovernanceParameters.MovingFundsData movingFundsData
```

### walletData

```solidity
struct BridgeGovernanceParameters.WalletData walletData
```

### fraudData

```solidity
struct BridgeGovernanceParameters.FraudData fraudData
```

### treasuryData

```solidity
struct BridgeGovernanceParameters.TreasuryData treasuryData
```

### bridge

```solidity
contract Bridge bridge
```

### governanceDelays

```solidity
uint256[3] governanceDelays
```

### bridgeGovernanceTransferChangeInitiated

```solidity
uint256 bridgeGovernanceTransferChangeInitiated
```

### newBridgeGovernance

```solidity
address newBridgeGovernance
```

### BridgeGovernanceTransferStarted

```solidity
event BridgeGovernanceTransferStarted(address newBridgeGovernance, uint256 timestamp)
```

### DepositDustThresholdUpdateStarted

```solidity
event DepositDustThresholdUpdateStarted(uint64 newDepositDustThreshold, uint256 timestamp)
```

### DepositDustThresholdUpdated

```solidity
event DepositDustThresholdUpdated(uint64 depositDustThreshold)
```

### DepositTreasuryFeeDivisorUpdateStarted

```solidity
event DepositTreasuryFeeDivisorUpdateStarted(uint64 depositTreasuryFeeDivisor, uint256 timestamp)
```

### DepositTreasuryFeeDivisorUpdated

```solidity
event DepositTreasuryFeeDivisorUpdated(uint64 depositTreasuryFeeDivisor)
```

### DepositTxMaxFeeUpdateStarted

```solidity
event DepositTxMaxFeeUpdateStarted(uint64 newDepositTxMaxFee, uint256 timestamp)
```

### DepositTxMaxFeeUpdated

```solidity
event DepositTxMaxFeeUpdated(uint64 depositTxMaxFee)
```

### DepositRevealAheadPeriodUpdateStarted

```solidity
event DepositRevealAheadPeriodUpdateStarted(uint32 newDepositRevealAheadPeriod, uint256 timestamp)
```

### DepositRevealAheadPeriodUpdated

```solidity
event DepositRevealAheadPeriodUpdated(uint32 depositRevealAheadPeriod)
```

### RedemptionDustThresholdUpdateStarted

```solidity
event RedemptionDustThresholdUpdateStarted(uint64 newRedemptionDustThreshold, uint256 timestamp)
```

### RedemptionDustThresholdUpdated

```solidity
event RedemptionDustThresholdUpdated(uint64 redemptionDustThreshold)
```

### RedemptionTreasuryFeeDivisorUpdateStarted

```solidity
event RedemptionTreasuryFeeDivisorUpdateStarted(uint64 newRedemptionTreasuryFeeDivisor, uint256 timestamp)
```

### RedemptionTreasuryFeeDivisorUpdated

```solidity
event RedemptionTreasuryFeeDivisorUpdated(uint64 redemptionTreasuryFeeDivisor)
```

### RedemptionTxMaxFeeUpdateStarted

```solidity
event RedemptionTxMaxFeeUpdateStarted(uint64 newRedemptionTxMaxFee, uint256 timestamp)
```

### RedemptionTxMaxFeeUpdated

```solidity
event RedemptionTxMaxFeeUpdated(uint64 redemptionTxMaxFee)
```

### RedemptionTxMaxTotalFeeUpdateStarted

```solidity
event RedemptionTxMaxTotalFeeUpdateStarted(uint64 newRedemptionTxMaxTotalFee, uint256 timestamp)
```

### RedemptionTxMaxTotalFeeUpdated

```solidity
event RedemptionTxMaxTotalFeeUpdated(uint64 redemptionTxMaxTotalFee)
```

### RedemptionTimeoutUpdateStarted

```solidity
event RedemptionTimeoutUpdateStarted(uint32 newRedemptionTimeout, uint256 timestamp)
```

### RedemptionTimeoutUpdated

```solidity
event RedemptionTimeoutUpdated(uint32 redemptionTimeout)
```

### RedemptionTimeoutSlashingAmountUpdateStarted

```solidity
event RedemptionTimeoutSlashingAmountUpdateStarted(uint96 newRedemptionTimeoutSlashingAmount, uint256 timestamp)
```

### RedemptionTimeoutSlashingAmountUpdated

```solidity
event RedemptionTimeoutSlashingAmountUpdated(uint96 redemptionTimeoutSlashingAmount)
```

### RedemptionTimeoutNotifierRewardMultiplierUpdateStarted

```solidity
event RedemptionTimeoutNotifierRewardMultiplierUpdateStarted(uint32 newRedemptionTimeoutNotifierRewardMultiplier, uint256 timestamp)
```

### RedemptionTimeoutNotifierRewardMultiplierUpdated

```solidity
event RedemptionTimeoutNotifierRewardMultiplierUpdated(uint32 redemptionTimeoutNotifierRewardMultiplier)
```

### MovingFundsTxMaxTotalFeeUpdateStarted

```solidity
event MovingFundsTxMaxTotalFeeUpdateStarted(uint64 newMovingFundsTxMaxTotalFee, uint256 timestamp)
```

### MovingFundsTxMaxTotalFeeUpdated

```solidity
event MovingFundsTxMaxTotalFeeUpdated(uint64 movingFundsTxMaxTotalFee)
```

### MovingFundsDustThresholdUpdateStarted

```solidity
event MovingFundsDustThresholdUpdateStarted(uint64 newMovingFundsDustThreshold, uint256 timestamp)
```

### MovingFundsDustThresholdUpdated

```solidity
event MovingFundsDustThresholdUpdated(uint64 movingFundsDustThreshold)
```

### MovingFundsTimeoutResetDelayUpdateStarted

```solidity
event MovingFundsTimeoutResetDelayUpdateStarted(uint32 newMovingFundsTimeoutResetDelay, uint256 timestamp)
```

### MovingFundsTimeoutResetDelayUpdated

```solidity
event MovingFundsTimeoutResetDelayUpdated(uint32 movingFundsTimeoutResetDelay)
```

### MovingFundsTimeoutUpdateStarted

```solidity
event MovingFundsTimeoutUpdateStarted(uint32 newMovingFundsTimeout, uint256 timestamp)
```

### MovingFundsTimeoutUpdated

```solidity
event MovingFundsTimeoutUpdated(uint32 movingFundsTimeout)
```

### MovingFundsTimeoutSlashingAmountUpdateStarted

```solidity
event MovingFundsTimeoutSlashingAmountUpdateStarted(uint96 newMovingFundsTimeoutSlashingAmount, uint256 timestamp)
```

### MovingFundsTimeoutSlashingAmountUpdated

```solidity
event MovingFundsTimeoutSlashingAmountUpdated(uint96 movingFundsTimeoutSlashingAmount)
```

### MovingFundsTimeoutNotifierRewardMultiplierUpdateStarted

```solidity
event MovingFundsTimeoutNotifierRewardMultiplierUpdateStarted(uint32 newMovingFundsTimeoutNotifierRewardMultiplier, uint256 timestamp)
```

### MovingFundsTimeoutNotifierRewardMultiplierUpdated

```solidity
event MovingFundsTimeoutNotifierRewardMultiplierUpdated(uint32 movingFundsTimeoutNotifierRewardMultiplier)
```

### MovingFundsCommitmentGasOffsetUpdateStarted

```solidity
event MovingFundsCommitmentGasOffsetUpdateStarted(uint16 newMovingFundsCommitmentGasOffset, uint256 timestamp)
```

### MovingFundsCommitmentGasOffsetUpdated

```solidity
event MovingFundsCommitmentGasOffsetUpdated(uint16 movingFundsCommitmentGasOffset)
```

### MovedFundsSweepTxMaxTotalFeeUpdateStarted

```solidity
event MovedFundsSweepTxMaxTotalFeeUpdateStarted(uint64 newMovedFundsSweepTxMaxTotalFee, uint256 timestamp)
```

### MovedFundsSweepTxMaxTotalFeeUpdated

```solidity
event MovedFundsSweepTxMaxTotalFeeUpdated(uint64 movedFundsSweepTxMaxTotalFee)
```

### MovedFundsSweepTimeoutUpdateStarted

```solidity
event MovedFundsSweepTimeoutUpdateStarted(uint32 newMovedFundsSweepTimeout, uint256 timestamp)
```

### MovedFundsSweepTimeoutUpdated

```solidity
event MovedFundsSweepTimeoutUpdated(uint32 movedFundsSweepTimeout)
```

### MovedFundsSweepTimeoutSlashingAmountUpdateStarted

```solidity
event MovedFundsSweepTimeoutSlashingAmountUpdateStarted(uint96 newMovedFundsSweepTimeoutSlashingAmount, uint256 timestamp)
```

### MovedFundsSweepTimeoutSlashingAmountUpdated

```solidity
event MovedFundsSweepTimeoutSlashingAmountUpdated(uint96 movedFundsSweepTimeoutSlashingAmount)
```

### MovedFundsSweepTimeoutNotifierRewardMultiplierUpdateStarted

```solidity
event MovedFundsSweepTimeoutNotifierRewardMultiplierUpdateStarted(uint32 newMovedFundsSweepTimeoutNotifierRewardMultiplier, uint256 timestamp)
```

### MovedFundsSweepTimeoutNotifierRewardMultiplierUpdated

```solidity
event MovedFundsSweepTimeoutNotifierRewardMultiplierUpdated(uint32 movedFundsSweepTimeoutNotifierRewardMultiplier)
```

### WalletCreationPeriodUpdateStarted

```solidity
event WalletCreationPeriodUpdateStarted(uint32 newWalletCreationPeriod, uint256 timestamp)
```

### WalletCreationPeriodUpdated

```solidity
event WalletCreationPeriodUpdated(uint32 walletCreationPeriod)
```

### WalletCreationMinBtcBalanceUpdateStarted

```solidity
event WalletCreationMinBtcBalanceUpdateStarted(uint64 newWalletCreationMinBtcBalance, uint256 timestamp)
```

### WalletCreationMinBtcBalanceUpdated

```solidity
event WalletCreationMinBtcBalanceUpdated(uint64 walletCreationMinBtcBalance)
```

### WalletCreationMaxBtcBalanceUpdateStarted

```solidity
event WalletCreationMaxBtcBalanceUpdateStarted(uint64 newWalletCreationMaxBtcBalance, uint256 timestamp)
```

### WalletCreationMaxBtcBalanceUpdated

```solidity
event WalletCreationMaxBtcBalanceUpdated(uint64 walletCreationMaxBtcBalance)
```

### WalletClosureMinBtcBalanceUpdateStarted

```solidity
event WalletClosureMinBtcBalanceUpdateStarted(uint64 newWalletClosureMinBtcBalance, uint256 timestamp)
```

### WalletClosureMinBtcBalanceUpdated

```solidity
event WalletClosureMinBtcBalanceUpdated(uint64 walletClosureMinBtcBalance)
```

### WalletMaxAgeUpdateStarted

```solidity
event WalletMaxAgeUpdateStarted(uint32 newWalletMaxAge, uint256 timestamp)
```

### WalletMaxAgeUpdated

```solidity
event WalletMaxAgeUpdated(uint32 walletMaxAge)
```

### WalletMaxBtcTransferUpdateStarted

```solidity
event WalletMaxBtcTransferUpdateStarted(uint64 newWalletMaxBtcTransfer, uint256 timestamp)
```

### WalletMaxBtcTransferUpdated

```solidity
event WalletMaxBtcTransferUpdated(uint64 walletMaxBtcTransfer)
```

### WalletClosingPeriodUpdateStarted

```solidity
event WalletClosingPeriodUpdateStarted(uint32 newWalletClosingPeriod, uint256 timestamp)
```

### WalletClosingPeriodUpdated

```solidity
event WalletClosingPeriodUpdated(uint32 walletClosingPeriod)
```

### FraudChallengeDepositAmountUpdateStarted

```solidity
event FraudChallengeDepositAmountUpdateStarted(uint96 newFraudChallengeDepositAmount, uint256 timestamp)
```

### FraudChallengeDepositAmountUpdated

```solidity
event FraudChallengeDepositAmountUpdated(uint96 fraudChallengeDepositAmount)
```

### FraudChallengeDefeatTimeoutUpdateStarted

```solidity
event FraudChallengeDefeatTimeoutUpdateStarted(uint32 newFraudChallengeDefeatTimeout, uint256 timestamp)
```

### FraudChallengeDefeatTimeoutUpdated

```solidity
event FraudChallengeDefeatTimeoutUpdated(uint32 fraudChallengeDefeatTimeout)
```

### FraudSlashingAmountUpdateStarted

```solidity
event FraudSlashingAmountUpdateStarted(uint96 newFraudSlashingAmount, uint256 timestamp)
```

### FraudSlashingAmountUpdated

```solidity
event FraudSlashingAmountUpdated(uint96 fraudSlashingAmount)
```

### FraudNotifierRewardMultiplierUpdateStarted

```solidity
event FraudNotifierRewardMultiplierUpdateStarted(uint32 newFraudNotifierRewardMultiplier, uint256 timestamp)
```

### FraudNotifierRewardMultiplierUpdated

```solidity
event FraudNotifierRewardMultiplierUpdated(uint32 fraudNotifierRewardMultiplier)
```

### TreasuryUpdateStarted

```solidity
event TreasuryUpdateStarted(address newTreasury, uint256 timestamp)
```

### TreasuryUpdated

```solidity
event TreasuryUpdated(address treasury)
```

### constructor

```solidity
constructor(contract Bridge _bridge, uint256 _governanceDelay) public
```

### setVaultStatus

```solidity
function setVaultStatus(address vault, bool isTrusted) external
```

Allows the Governance to mark the given vault address as trusted
or no longer trusted. Vaults are not trusted by default.
Trusted vault must meet the following criteria:
- `IVault.receiveBalanceIncrease` must have a known, low gas
cost,
- `IVault.receiveBalanceIncrease` must never revert.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| vault | address | The address of the vault. |
| isTrusted | bool | flag indicating whether the vault is trusted or not. |

### setSpvMaintainerStatus

```solidity
function setSpvMaintainerStatus(address spvMaintainer, bool isTrusted) external
```

Allows the Governance to mark the given address as trusted
or no longer trusted SPV maintainer. Addresses are not trusted
as SPV maintainers by default.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| spvMaintainer | address | The address of the SPV maintainer. |
| isTrusted | bool | flag indicating whether the address is trusted or not. |

### beginGovernanceDelayUpdate

```solidity
function beginGovernanceDelayUpdate(uint256 _newGovernanceDelay) external
```

Begins the governance delay update process.

Can be called only by the contract owner. The event that informs about
the start of the governance delay was skipped on purpose to trim
the contract size. All the params inside of the `governanceDelays`
array are public and can be easily fetched.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newGovernanceDelay | uint256 | New governance delay |

### finalizeGovernanceDelayUpdate

```solidity
function finalizeGovernanceDelayUpdate() external
```

Finalizes the governance delay update process.

Can be called only by the contract owner, after the governance
delay elapses. Updated event was skipped on purpose to trim the
contract size. All the params inside of the `governanceDelays`
array are public and can be easily fetched.

### beginBridgeGovernanceTransfer

```solidity
function beginBridgeGovernanceTransfer(address _newBridgeGovernance) external
```

Begins the Bridge governance transfer process.

Can be called only by the contract owner. It is the governance
responsibility to validate the correctness of the new Bridge
Governance contract. The other reason for not adding this check is
to go down with the contract size and leaving only the essential code.

### finalizeBridgeGovernanceTransfer

```solidity
function finalizeBridgeGovernanceTransfer() external
```

Finalizes the bridge governance transfer process.

Can be called only by the contract owner, after the governance
delay elapses. Bridge governance transferred event can be read
from the Governable bridge contract 'GovernanceTransferred(old, new)'.
Event that informs about the transfer in this function is skipped on
purpose to go down with the contract size.

### beginDepositDustThresholdUpdate

```solidity
function beginDepositDustThresholdUpdate(uint64 _newDepositDustThreshold) external
```

Begins the deposit dust threshold amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDepositDustThreshold | uint64 | New deposit dust threshold amount. |

### finalizeDepositDustThresholdUpdate

```solidity
function finalizeDepositDustThresholdUpdate() external
```

Finalizes the deposit dust threshold amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginDepositTreasuryFeeDivisorUpdate

```solidity
function beginDepositTreasuryFeeDivisorUpdate(uint64 _newDepositTreasuryFeeDivisor) external
```

Begins the deposit treasury fee divisor amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDepositTreasuryFeeDivisor | uint64 | New deposit treasury fee divisor. |

### finalizeDepositTreasuryFeeDivisorUpdate

```solidity
function finalizeDepositTreasuryFeeDivisorUpdate() external
```

Finalizes the deposit treasury fee divisor amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginDepositTxMaxFeeUpdate

```solidity
function beginDepositTxMaxFeeUpdate(uint64 _newDepositTxMaxFee) external
```

Begins the deposit tx max fee amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDepositTxMaxFee | uint64 | New deposit tx max fee. |

### finalizeDepositTxMaxFeeUpdate

```solidity
function finalizeDepositTxMaxFeeUpdate() external
```

Finalizes the deposit tx max fee amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginDepositRevealAheadPeriodUpdate

```solidity
function beginDepositRevealAheadPeriodUpdate(uint32 _newDepositRevealAheadPeriod) external
```

Begins the deposit reveal ahead period update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newDepositRevealAheadPeriod | uint32 | New deposit reveal ahead period. |

### finalizeDepositRevealAheadPeriodUpdate

```solidity
function finalizeDepositRevealAheadPeriodUpdate() external
```

Finalizes the deposit reveal ahead period update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRedemptionDustThresholdUpdate

```solidity
function beginRedemptionDustThresholdUpdate(uint64 _newRedemptionDustThreshold) external
```

Begins the redemption dust threshold amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRedemptionDustThreshold | uint64 | New redemption dust threshold. |

### finalizeRedemptionDustThresholdUpdate

```solidity
function finalizeRedemptionDustThresholdUpdate() external
```

Finalizes the dust threshold amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRedemptionTreasuryFeeDivisorUpdate

```solidity
function beginRedemptionTreasuryFeeDivisorUpdate(uint64 _newRedemptionTreasuryFeeDivisor) external
```

Begins the redemption treasury fee divisor amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRedemptionTreasuryFeeDivisor | uint64 | New redemption treasury fee divisor. |

### finalizeRedemptionTreasuryFeeDivisorUpdate

```solidity
function finalizeRedemptionTreasuryFeeDivisorUpdate() external
```

Finalizes the redemption treasury fee divisor amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRedemptionTxMaxFeeUpdate

```solidity
function beginRedemptionTxMaxFeeUpdate(uint64 _newRedemptionTxMaxFee) external
```

Begins the redemption tx max fee amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRedemptionTxMaxFee | uint64 | New redemption tx max fee. |

### finalizeRedemptionTxMaxFeeUpdate

```solidity
function finalizeRedemptionTxMaxFeeUpdate() external
```

Finalizes the redemption tx max fee amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRedemptionTxMaxTotalFeeUpdate

```solidity
function beginRedemptionTxMaxTotalFeeUpdate(uint64 _newRedemptionTxMaxTotalFee) external
```

Begins the redemption tx max total fee amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRedemptionTxMaxTotalFee | uint64 | New redemption tx max total fee. |

### finalizeRedemptionTxMaxTotalFeeUpdate

```solidity
function finalizeRedemptionTxMaxTotalFeeUpdate() external
```

Finalizes the redemption tx max total fee amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRedemptionTimeoutUpdate

```solidity
function beginRedemptionTimeoutUpdate(uint32 _newRedemptionTimeout) external
```

Begins the redemption timeout amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRedemptionTimeout | uint32 | New redemption timeout. |

### finalizeRedemptionTimeoutUpdate

```solidity
function finalizeRedemptionTimeoutUpdate() external
```

Finalizes the redemption timeout amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRedemptionTimeoutSlashingAmountUpdate

```solidity
function beginRedemptionTimeoutSlashingAmountUpdate(uint96 _newRedemptionTimeoutSlashingAmount) external
```

Begins the redemption timeout slashing amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRedemptionTimeoutSlashingAmount | uint96 | New redemption timeout slashing amount. |

### finalizeRedemptionTimeoutSlashingAmountUpdate

```solidity
function finalizeRedemptionTimeoutSlashingAmountUpdate() external
```

Finalizes the redemption timeout slashing amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginRedemptionTimeoutNotifierRewardMultiplierUpdate

```solidity
function beginRedemptionTimeoutNotifierRewardMultiplierUpdate(uint32 _newRedemptionTimeoutNotifierRewardMultiplier) external
```

Begins the redemption timeout notifier reward multiplier amount
update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newRedemptionTimeoutNotifierRewardMultiplier | uint32 | New redemption timeout notifier reward multiplier. |

### finalizeRedemptionTimeoutNotifierRewardMultiplierUpdate

```solidity
function finalizeRedemptionTimeoutNotifierRewardMultiplierUpdate() external
```

Finalizes the redemption timeout notifier reward multiplier amount
update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovingFundsTxMaxTotalFeeUpdate

```solidity
function beginMovingFundsTxMaxTotalFeeUpdate(uint64 _newMovingFundsTxMaxTotalFee) external
```

Begins the moving funds tx max total fee update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovingFundsTxMaxTotalFee | uint64 | New moving funds tx max total fee. |

### finalizeMovingFundsTxMaxTotalFeeUpdate

```solidity
function finalizeMovingFundsTxMaxTotalFeeUpdate() external
```

Finalizes the moving funds tx max total fee update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovingFundsDustThresholdUpdate

```solidity
function beginMovingFundsDustThresholdUpdate(uint64 _newMovingFundsDustThreshold) external
```

Begins the moving funds dust threshold update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovingFundsDustThreshold | uint64 | New moving funds dust threshold. |

### finalizeMovingFundsDustThresholdUpdate

```solidity
function finalizeMovingFundsDustThresholdUpdate() external
```

Finalizes the moving funds dust threshold update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovingFundsTimeoutResetDelayUpdate

```solidity
function beginMovingFundsTimeoutResetDelayUpdate(uint32 _newMovingFundsTimeoutResetDelay) external
```

Begins the moving funds timeout reset delay update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovingFundsTimeoutResetDelay | uint32 | New moving funds timeout reset delay. |

### finalizeMovingFundsTimeoutResetDelayUpdate

```solidity
function finalizeMovingFundsTimeoutResetDelayUpdate() external
```

Finalizes the moving funds timeout reset delay update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovingFundsTimeoutUpdate

```solidity
function beginMovingFundsTimeoutUpdate(uint32 _newMovingFundsTimeout) external
```

Begins the moving funds timeout update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovingFundsTimeout | uint32 | New moving funds timeout. |

### finalizeMovingFundsTimeoutUpdate

```solidity
function finalizeMovingFundsTimeoutUpdate() external
```

Finalizes the moving funds timeout update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovingFundsTimeoutSlashingAmountUpdate

```solidity
function beginMovingFundsTimeoutSlashingAmountUpdate(uint96 _newMovingFundsTimeoutSlashingAmount) external
```

Begins the moving funds timeout slashing amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovingFundsTimeoutSlashingAmount | uint96 | New moving funds timeout slashing amount. |

### finalizeMovingFundsTimeoutSlashingAmountUpdate

```solidity
function finalizeMovingFundsTimeoutSlashingAmountUpdate() external
```

Finalizes the moving funds timeout slashing amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovingFundsTimeoutNotifierRewardMultiplierUpdate

```solidity
function beginMovingFundsTimeoutNotifierRewardMultiplierUpdate(uint32 _newMovingFundsTimeoutNotifierRewardMultiplier) external
```

Begins the moving funds timeout notifier reward multiplier update
process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovingFundsTimeoutNotifierRewardMultiplier | uint32 | New moving funds timeout notifier reward multiplier. |

### finalizeMovingFundsTimeoutNotifierRewardMultiplierUpdate

```solidity
function finalizeMovingFundsTimeoutNotifierRewardMultiplierUpdate() external
```

Finalizes the moving funds timeout notifier reward multiplier
update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovingFundsCommitmentGasOffsetUpdate

```solidity
function beginMovingFundsCommitmentGasOffsetUpdate(uint16 _newMovingFundsCommitmentGasOffset) external
```

Begins the moving funds commitment gas offset update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovingFundsCommitmentGasOffset | uint16 | New moving funds commitment gas offset. |

### finalizeMovingFundsCommitmentGasOffsetUpdate

```solidity
function finalizeMovingFundsCommitmentGasOffsetUpdate() external
```

Finalizes the moving funds commitment gas offset update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovedFundsSweepTxMaxTotalFeeUpdate

```solidity
function beginMovedFundsSweepTxMaxTotalFeeUpdate(uint64 _newMovedFundsSweepTxMaxTotalFee) external
```

Begins the moved funds sweep tx max total fee update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovedFundsSweepTxMaxTotalFee | uint64 | New moved funds sweep tx max total fee. |

### finalizeMovedFundsSweepTxMaxTotalFeeUpdate

```solidity
function finalizeMovedFundsSweepTxMaxTotalFeeUpdate() external
```

Finalizes the moved funds sweep tx max total fee update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovedFundsSweepTimeoutUpdate

```solidity
function beginMovedFundsSweepTimeoutUpdate(uint32 _newMovedFundsSweepTimeout) external
```

Begins the moved funds sweep timeout update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovedFundsSweepTimeout | uint32 | New moved funds sweep timeout. |

### finalizeMovedFundsSweepTimeoutUpdate

```solidity
function finalizeMovedFundsSweepTimeoutUpdate() external
```

Finalizes the moved funds sweep timeout update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovedFundsSweepTimeoutSlashingAmountUpdate

```solidity
function beginMovedFundsSweepTimeoutSlashingAmountUpdate(uint96 _newMovedFundsSweepTimeoutSlashingAmount) external
```

Begins the moved funds sweep timeout slashing amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovedFundsSweepTimeoutSlashingAmount | uint96 | New moved funds sweep timeout slashing amount. |

### finalizeMovedFundsSweepTimeoutSlashingAmountUpdate

```solidity
function finalizeMovedFundsSweepTimeoutSlashingAmountUpdate() external
```

Finalizes the moved funds sweep timeout slashing amount update
process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate

```solidity
function beginMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate(uint32 _newMovedFundsSweepTimeoutNotifierRewardMultiplier) external
```

Begins the moved funds sweep timeout notifier reward multiplier
update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newMovedFundsSweepTimeoutNotifierRewardMultiplier | uint32 | New moved funds sweep timeout notifier reward multiplier. |

### finalizeMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate

```solidity
function finalizeMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate() external
```

Finalizes the moved funds sweep timeout notifier reward multiplier
update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginWalletCreationPeriodUpdate

```solidity
function beginWalletCreationPeriodUpdate(uint32 _newWalletCreationPeriod) external
```

Begins the wallet creation period update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newWalletCreationPeriod | uint32 | New wallet creation period. |

### finalizeWalletCreationPeriodUpdate

```solidity
function finalizeWalletCreationPeriodUpdate() external
```

Finalizes the wallet creation period update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginWalletCreationMinBtcBalanceUpdate

```solidity
function beginWalletCreationMinBtcBalanceUpdate(uint64 _newWalletCreationMinBtcBalance) external
```

Begins the wallet creation min btc balance update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newWalletCreationMinBtcBalance | uint64 | New wallet creation min btc balance. |

### finalizeWalletCreationMinBtcBalanceUpdate

```solidity
function finalizeWalletCreationMinBtcBalanceUpdate() external
```

Finalizes the wallet creation min btc balance update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginWalletCreationMaxBtcBalanceUpdate

```solidity
function beginWalletCreationMaxBtcBalanceUpdate(uint64 _newWalletCreationMaxBtcBalance) external
```

Begins the wallet creation max btc balance update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newWalletCreationMaxBtcBalance | uint64 | New wallet creation max btc balance. |

### finalizeWalletCreationMaxBtcBalanceUpdate

```solidity
function finalizeWalletCreationMaxBtcBalanceUpdate() external
```

Finalizes the wallet creation max btc balance update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginWalletClosureMinBtcBalanceUpdate

```solidity
function beginWalletClosureMinBtcBalanceUpdate(uint64 _newWalletClosureMinBtcBalance) external
```

Begins the wallet closure min btc balance update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newWalletClosureMinBtcBalance | uint64 | New wallet closure min btc balance. |

### finalizeWalletClosureMinBtcBalanceUpdate

```solidity
function finalizeWalletClosureMinBtcBalanceUpdate() external
```

Finalizes the wallet closure min btc balance update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginWalletMaxAgeUpdate

```solidity
function beginWalletMaxAgeUpdate(uint32 _newWalletMaxAge) external
```

Begins the wallet max age update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newWalletMaxAge | uint32 | New wallet max age. |

### finalizeWalletMaxAgeUpdate

```solidity
function finalizeWalletMaxAgeUpdate() external
```

Finalizes the wallet max age update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginWalletMaxBtcTransferUpdate

```solidity
function beginWalletMaxBtcTransferUpdate(uint64 _newWalletMaxBtcTransfer) external
```

Begins the wallet max btc transfer amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newWalletMaxBtcTransfer | uint64 | New wallet max btc transfer. |

### finalizeWalletMaxBtcTransferUpdate

```solidity
function finalizeWalletMaxBtcTransferUpdate() external
```

Finalizes the wallet max btc transfer amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginWalletClosingPeriodUpdate

```solidity
function beginWalletClosingPeriodUpdate(uint32 _newWalletClosingPeriod) external
```

Begins the wallet closing period update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newWalletClosingPeriod | uint32 | New wallet closing period. |

### finalizeWalletClosingPeriodUpdate

```solidity
function finalizeWalletClosingPeriodUpdate() external
```

Finalizes the wallet closing period update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginFraudChallengeDepositAmountUpdate

```solidity
function beginFraudChallengeDepositAmountUpdate(uint96 _newFraudChallengeDepositAmount) external
```

Begins the fraud challenge deposit amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newFraudChallengeDepositAmount | uint96 | New fraud challenge deposit amount. |

### finalizeFraudChallengeDepositAmountUpdate

```solidity
function finalizeFraudChallengeDepositAmountUpdate() external
```

Finalizes the fraud challenge deposit amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginFraudChallengeDefeatTimeoutUpdate

```solidity
function beginFraudChallengeDefeatTimeoutUpdate(uint32 _newFraudChallengeDefeatTimeout) external
```

Begins the fraud challenge defeat timeout update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newFraudChallengeDefeatTimeout | uint32 | New fraud challenge defeat timeout. |

### finalizeFraudChallengeDefeatTimeoutUpdate

```solidity
function finalizeFraudChallengeDefeatTimeoutUpdate() external
```

Finalizes the fraud challenge defeat timeout update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginFraudSlashingAmountUpdate

```solidity
function beginFraudSlashingAmountUpdate(uint96 _newFraudSlashingAmount) external
```

Begins the fraud slashing amount update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newFraudSlashingAmount | uint96 | New fraud slashing amount. |

### finalizeFraudSlashingAmountUpdate

```solidity
function finalizeFraudSlashingAmountUpdate() external
```

Finalizes the fraud slashing amount update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginFraudNotifierRewardMultiplierUpdate

```solidity
function beginFraudNotifierRewardMultiplierUpdate(uint32 _newFraudNotifierRewardMultiplier) external
```

Begins the fraud notifier reward multiplier update process.

Can be called only by the contract owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newFraudNotifierRewardMultiplier | uint32 | New fraud notifier reward multiplier. |

### finalizeFraudNotifierRewardMultiplierUpdate

```solidity
function finalizeFraudNotifierRewardMultiplierUpdate() external
```

Finalizes the fraud notifier reward multiplier update process.

Can be called only by the contract owner, after the governance
delay elapses.

### beginTreasuryUpdate

```solidity
function beginTreasuryUpdate(address _newTreasury) external
```

Begins the treasury address update process.

Can be called only by the contract owner. It does not perform
any parameter validation.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newTreasury | address | New treasury address. |

### finalizeTreasuryUpdate

```solidity
function finalizeTreasuryUpdate() external
```

Finalizes the treasury address update process.

Can be called only by the contract owner, after the governance
delay elapses.

### governanceDelay

```solidity
function governanceDelay() internal view returns (uint256)
```

Gets the governance delay parameter.

## BridgeGovernanceParameters

### TreasuryData

```solidity
struct TreasuryData {
  address newTreasury;
  uint256 treasuryChangeInitiated;
}
```

### DepositData

```solidity
struct DepositData {
  uint64 newDepositDustThreshold;
  uint256 depositDustThresholdChangeInitiated;
  uint64 newDepositTreasuryFeeDivisor;
  uint256 depositTreasuryFeeDivisorChangeInitiated;
  uint64 newDepositTxMaxFee;
  uint256 depositTxMaxFeeChangeInitiated;
  uint32 newDepositRevealAheadPeriod;
  uint256 depositRevealAheadPeriodChangeInitiated;
}
```

### RedemptionData

```solidity
struct RedemptionData {
  uint64 newRedemptionDustThreshold;
  uint256 redemptionDustThresholdChangeInitiated;
  uint64 newRedemptionTreasuryFeeDivisor;
  uint256 redemptionTreasuryFeeDivisorChangeInitiated;
  uint64 newRedemptionTxMaxFee;
  uint256 redemptionTxMaxFeeChangeInitiated;
  uint64 newRedemptionTxMaxTotalFee;
  uint256 redemptionTxMaxTotalFeeChangeInitiated;
  uint32 newRedemptionTimeout;
  uint256 redemptionTimeoutChangeInitiated;
  uint96 newRedemptionTimeoutSlashingAmount;
  uint256 redemptionTimeoutSlashingAmountChangeInitiated;
  uint32 newRedemptionTimeoutNotifierRewardMultiplier;
  uint256 redemptionTimeoutNotifierRewardMultiplierChangeInitiated;
}
```

### MovingFundsData

```solidity
struct MovingFundsData {
  uint64 newMovingFundsTxMaxTotalFee;
  uint256 movingFundsTxMaxTotalFeeChangeInitiated;
  uint64 newMovingFundsDustThreshold;
  uint256 movingFundsDustThresholdChangeInitiated;
  uint32 newMovingFundsTimeoutResetDelay;
  uint256 movingFundsTimeoutResetDelayChangeInitiated;
  uint32 newMovingFundsTimeout;
  uint256 movingFundsTimeoutChangeInitiated;
  uint96 newMovingFundsTimeoutSlashingAmount;
  uint256 movingFundsTimeoutSlashingAmountChangeInitiated;
  uint32 newMovingFundsTimeoutNotifierRewardMultiplier;
  uint256 movingFundsTimeoutNotifierRewardMultiplierChangeInitiated;
  uint16 newMovingFundsCommitmentGasOffset;
  uint256 movingFundsCommitmentGasOffsetChangeInitiated;
  uint64 newMovedFundsSweepTxMaxTotalFee;
  uint256 movedFundsSweepTxMaxTotalFeeChangeInitiated;
  uint32 newMovedFundsSweepTimeout;
  uint256 movedFundsSweepTimeoutChangeInitiated;
  uint96 newMovedFundsSweepTimeoutSlashingAmount;
  uint256 movedFundsSweepTimeoutSlashingAmountChangeInitiated;
  uint32 newMovedFundsSweepTimeoutNotifierRewardMultiplier;
  uint256 movedFundsSweepTimeoutNotifierRewardMultiplierChangeInitiated;
}
```

### WalletData

```solidity
struct WalletData {
  uint32 newWalletCreationPeriod;
  uint256 walletCreationPeriodChangeInitiated;
  uint64 newWalletCreationMinBtcBalance;
  uint256 walletCreationMinBtcBalanceChangeInitiated;
  uint64 newWalletCreationMaxBtcBalance;
  uint256 walletCreationMaxBtcBalanceChangeInitiated;
  uint64 newWalletClosureMinBtcBalance;
  uint256 walletClosureMinBtcBalanceChangeInitiated;
  uint32 newWalletMaxAge;
  uint256 walletMaxAgeChangeInitiated;
  uint64 newWalletMaxBtcTransfer;
  uint256 walletMaxBtcTransferChangeInitiated;
  uint32 newWalletClosingPeriod;
  uint256 walletClosingPeriodChangeInitiated;
}
```

### FraudData

```solidity
struct FraudData {
  uint96 newFraudChallengeDepositAmount;
  uint256 fraudChallengeDepositAmountChangeInitiated;
  uint32 newFraudChallengeDefeatTimeout;
  uint256 fraudChallengeDefeatTimeoutChangeInitiated;
  uint96 newFraudSlashingAmount;
  uint256 fraudSlashingAmountChangeInitiated;
  uint32 newFraudNotifierRewardMultiplier;
  uint256 fraudNotifierRewardMultiplierChangeInitiated;
}
```

### DepositDustThresholdUpdateStarted

```solidity
event DepositDustThresholdUpdateStarted(uint64 newDepositDustThreshold, uint256 timestamp)
```

### DepositDustThresholdUpdated

```solidity
event DepositDustThresholdUpdated(uint64 depositDustThreshold)
```

### DepositTreasuryFeeDivisorUpdateStarted

```solidity
event DepositTreasuryFeeDivisorUpdateStarted(uint64 depositTreasuryFeeDivisor, uint256 timestamp)
```

### DepositTreasuryFeeDivisorUpdated

```solidity
event DepositTreasuryFeeDivisorUpdated(uint64 depositTreasuryFeeDivisor)
```

### DepositTxMaxFeeUpdateStarted

```solidity
event DepositTxMaxFeeUpdateStarted(uint64 newDepositTxMaxFee, uint256 timestamp)
```

### DepositTxMaxFeeUpdated

```solidity
event DepositTxMaxFeeUpdated(uint64 depositTxMaxFee)
```

### DepositRevealAheadPeriodUpdateStarted

```solidity
event DepositRevealAheadPeriodUpdateStarted(uint32 newDepositRevealAheadPeriod, uint256 timestamp)
```

### DepositRevealAheadPeriodUpdated

```solidity
event DepositRevealAheadPeriodUpdated(uint32 depositRevealAheadPeriod)
```

### RedemptionDustThresholdUpdateStarted

```solidity
event RedemptionDustThresholdUpdateStarted(uint64 newRedemptionDustThreshold, uint256 timestamp)
```

### RedemptionDustThresholdUpdated

```solidity
event RedemptionDustThresholdUpdated(uint64 redemptionDustThreshold)
```

### RedemptionTreasuryFeeDivisorUpdateStarted

```solidity
event RedemptionTreasuryFeeDivisorUpdateStarted(uint64 newRedemptionTreasuryFeeDivisor, uint256 timestamp)
```

### RedemptionTreasuryFeeDivisorUpdated

```solidity
event RedemptionTreasuryFeeDivisorUpdated(uint64 redemptionTreasuryFeeDivisor)
```

### RedemptionTxMaxFeeUpdateStarted

```solidity
event RedemptionTxMaxFeeUpdateStarted(uint64 newRedemptionTxMaxFee, uint256 timestamp)
```

### RedemptionTxMaxFeeUpdated

```solidity
event RedemptionTxMaxFeeUpdated(uint64 redemptionTxMaxFee)
```

### RedemptionTxMaxTotalFeeUpdateStarted

```solidity
event RedemptionTxMaxTotalFeeUpdateStarted(uint64 newRedemptionTxMaxTotalFee, uint256 timestamp)
```

### RedemptionTxMaxTotalFeeUpdated

```solidity
event RedemptionTxMaxTotalFeeUpdated(uint64 redemptionTxMaxTotalFee)
```

### RedemptionTimeoutUpdateStarted

```solidity
event RedemptionTimeoutUpdateStarted(uint32 newRedemptionTimeout, uint256 timestamp)
```

### RedemptionTimeoutUpdated

```solidity
event RedemptionTimeoutUpdated(uint32 redemptionTimeout)
```

### RedemptionTimeoutSlashingAmountUpdateStarted

```solidity
event RedemptionTimeoutSlashingAmountUpdateStarted(uint96 newRedemptionTimeoutSlashingAmount, uint256 timestamp)
```

### RedemptionTimeoutSlashingAmountUpdated

```solidity
event RedemptionTimeoutSlashingAmountUpdated(uint96 redemptionTimeoutSlashingAmount)
```

### RedemptionTimeoutNotifierRewardMultiplierUpdateStarted

```solidity
event RedemptionTimeoutNotifierRewardMultiplierUpdateStarted(uint32 newRedemptionTimeoutNotifierRewardMultiplier, uint256 timestamp)
```

### RedemptionTimeoutNotifierRewardMultiplierUpdated

```solidity
event RedemptionTimeoutNotifierRewardMultiplierUpdated(uint32 redemptionTimeoutNotifierRewardMultiplier)
```

### MovingFundsTxMaxTotalFeeUpdateStarted

```solidity
event MovingFundsTxMaxTotalFeeUpdateStarted(uint64 newMovingFundsTxMaxTotalFee, uint256 timestamp)
```

### MovingFundsTxMaxTotalFeeUpdated

```solidity
event MovingFundsTxMaxTotalFeeUpdated(uint64 movingFundsTxMaxTotalFee)
```

### MovingFundsDustThresholdUpdateStarted

```solidity
event MovingFundsDustThresholdUpdateStarted(uint64 newMovingFundsDustThreshold, uint256 timestamp)
```

### MovingFundsDustThresholdUpdated

```solidity
event MovingFundsDustThresholdUpdated(uint64 movingFundsDustThreshold)
```

### MovingFundsTimeoutResetDelayUpdateStarted

```solidity
event MovingFundsTimeoutResetDelayUpdateStarted(uint32 newMovingFundsTimeoutResetDelay, uint256 timestamp)
```

### MovingFundsTimeoutResetDelayUpdated

```solidity
event MovingFundsTimeoutResetDelayUpdated(uint32 movingFundsTimeoutResetDelay)
```

### MovingFundsTimeoutUpdateStarted

```solidity
event MovingFundsTimeoutUpdateStarted(uint32 newMovingFundsTimeout, uint256 timestamp)
```

### MovingFundsTimeoutUpdated

```solidity
event MovingFundsTimeoutUpdated(uint32 movingFundsTimeout)
```

### MovingFundsTimeoutSlashingAmountUpdateStarted

```solidity
event MovingFundsTimeoutSlashingAmountUpdateStarted(uint96 newMovingFundsTimeoutSlashingAmount, uint256 timestamp)
```

### MovingFundsTimeoutSlashingAmountUpdated

```solidity
event MovingFundsTimeoutSlashingAmountUpdated(uint96 movingFundsTimeoutSlashingAmount)
```

### MovingFundsTimeoutNotifierRewardMultiplierUpdateStarted

```solidity
event MovingFundsTimeoutNotifierRewardMultiplierUpdateStarted(uint32 newMovingFundsTimeoutNotifierRewardMultiplier, uint256 timestamp)
```

### MovingFundsTimeoutNotifierRewardMultiplierUpdated

```solidity
event MovingFundsTimeoutNotifierRewardMultiplierUpdated(uint32 movingFundsTimeoutNotifierRewardMultiplier)
```

### MovingFundsCommitmentGasOffsetUpdateStarted

```solidity
event MovingFundsCommitmentGasOffsetUpdateStarted(uint16 newMovingFundsCommitmentGasOffset, uint256 timestamp)
```

### MovingFundsCommitmentGasOffsetUpdated

```solidity
event MovingFundsCommitmentGasOffsetUpdated(uint16 movingFundsCommitmentGasOffset)
```

### MovedFundsSweepTxMaxTotalFeeUpdateStarted

```solidity
event MovedFundsSweepTxMaxTotalFeeUpdateStarted(uint64 newMovedFundsSweepTxMaxTotalFee, uint256 timestamp)
```

### MovedFundsSweepTxMaxTotalFeeUpdated

```solidity
event MovedFundsSweepTxMaxTotalFeeUpdated(uint64 movedFundsSweepTxMaxTotalFee)
```

### MovedFundsSweepTimeoutUpdateStarted

```solidity
event MovedFundsSweepTimeoutUpdateStarted(uint32 newMovedFundsSweepTimeout, uint256 timestamp)
```

### MovedFundsSweepTimeoutUpdated

```solidity
event MovedFundsSweepTimeoutUpdated(uint32 movedFundsSweepTimeout)
```

### MovedFundsSweepTimeoutSlashingAmountUpdateStarted

```solidity
event MovedFundsSweepTimeoutSlashingAmountUpdateStarted(uint96 newMovedFundsSweepTimeoutSlashingAmount, uint256 timestamp)
```

### MovedFundsSweepTimeoutSlashingAmountUpdated

```solidity
event MovedFundsSweepTimeoutSlashingAmountUpdated(uint96 movedFundsSweepTimeoutSlashingAmount)
```

### MovedFundsSweepTimeoutNotifierRewardMultiplierUpdateStarted

```solidity
event MovedFundsSweepTimeoutNotifierRewardMultiplierUpdateStarted(uint32 newMovedFundsSweepTimeoutNotifierRewardMultiplier, uint256 timestamp)
```

### MovedFundsSweepTimeoutNotifierRewardMultiplierUpdated

```solidity
event MovedFundsSweepTimeoutNotifierRewardMultiplierUpdated(uint32 movedFundsSweepTimeoutNotifierRewardMultiplier)
```

### WalletCreationPeriodUpdateStarted

```solidity
event WalletCreationPeriodUpdateStarted(uint32 newWalletCreationPeriod, uint256 timestamp)
```

### WalletCreationPeriodUpdated

```solidity
event WalletCreationPeriodUpdated(uint32 walletCreationPeriod)
```

### WalletCreationMinBtcBalanceUpdateStarted

```solidity
event WalletCreationMinBtcBalanceUpdateStarted(uint64 newWalletCreationMinBtcBalance, uint256 timestamp)
```

### WalletCreationMinBtcBalanceUpdated

```solidity
event WalletCreationMinBtcBalanceUpdated(uint64 walletCreationMinBtcBalance)
```

### WalletCreationMaxBtcBalanceUpdateStarted

```solidity
event WalletCreationMaxBtcBalanceUpdateStarted(uint64 newWalletCreationMaxBtcBalance, uint256 timestamp)
```

### WalletCreationMaxBtcBalanceUpdated

```solidity
event WalletCreationMaxBtcBalanceUpdated(uint64 walletCreationMaxBtcBalance)
```

### WalletClosureMinBtcBalanceUpdateStarted

```solidity
event WalletClosureMinBtcBalanceUpdateStarted(uint64 newWalletClosureMinBtcBalance, uint256 timestamp)
```

### WalletClosureMinBtcBalanceUpdated

```solidity
event WalletClosureMinBtcBalanceUpdated(uint64 walletClosureMinBtcBalance)
```

### WalletMaxAgeUpdateStarted

```solidity
event WalletMaxAgeUpdateStarted(uint32 newWalletMaxAge, uint256 timestamp)
```

### WalletMaxAgeUpdated

```solidity
event WalletMaxAgeUpdated(uint32 walletMaxAge)
```

### WalletMaxBtcTransferUpdateStarted

```solidity
event WalletMaxBtcTransferUpdateStarted(uint64 newWalletMaxBtcTransfer, uint256 timestamp)
```

### WalletMaxBtcTransferUpdated

```solidity
event WalletMaxBtcTransferUpdated(uint64 walletMaxBtcTransfer)
```

### WalletClosingPeriodUpdateStarted

```solidity
event WalletClosingPeriodUpdateStarted(uint32 newWalletClosingPeriod, uint256 timestamp)
```

### WalletClosingPeriodUpdated

```solidity
event WalletClosingPeriodUpdated(uint32 walletClosingPeriod)
```

### FraudChallengeDepositAmountUpdateStarted

```solidity
event FraudChallengeDepositAmountUpdateStarted(uint96 newFraudChallengeDepositAmount, uint256 timestamp)
```

### FraudChallengeDepositAmountUpdated

```solidity
event FraudChallengeDepositAmountUpdated(uint96 fraudChallengeDepositAmount)
```

### FraudChallengeDefeatTimeoutUpdateStarted

```solidity
event FraudChallengeDefeatTimeoutUpdateStarted(uint32 newFraudChallengeDefeatTimeout, uint256 timestamp)
```

### FraudChallengeDefeatTimeoutUpdated

```solidity
event FraudChallengeDefeatTimeoutUpdated(uint32 fraudChallengeDefeatTimeout)
```

### FraudSlashingAmountUpdateStarted

```solidity
event FraudSlashingAmountUpdateStarted(uint96 newFraudSlashingAmount, uint256 timestamp)
```

### FraudSlashingAmountUpdated

```solidity
event FraudSlashingAmountUpdated(uint96 fraudSlashingAmount)
```

### FraudNotifierRewardMultiplierUpdateStarted

```solidity
event FraudNotifierRewardMultiplierUpdateStarted(uint32 newFraudNotifierRewardMultiplier, uint256 timestamp)
```

### FraudNotifierRewardMultiplierUpdated

```solidity
event FraudNotifierRewardMultiplierUpdated(uint32 fraudNotifierRewardMultiplier)
```

### TreasuryUpdateStarted

```solidity
event TreasuryUpdateStarted(address newTreasury, uint256 timestamp)
```

### TreasuryUpdated

```solidity
event TreasuryUpdated(address treasury)
```

### onlyAfterGovernanceDelay

```solidity
modifier onlyAfterGovernanceDelay(uint256 changeInitiatedTimestamp, uint256 governanceDelay)
```

Reverts if called before the governance delay elapses.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| changeInitiatedTimestamp | uint256 | Timestamp indicating the beginning of the change. |
| governanceDelay | uint256 |  |

### beginDepositDustThresholdUpdate

```solidity
function beginDepositDustThresholdUpdate(struct BridgeGovernanceParameters.DepositData self, uint64 _newDepositDustThreshold) external
```

Begins the deposit dust threshold amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.DepositData |  |
| _newDepositDustThreshold | uint64 | New deposit dust threshold amount. |

### finalizeDepositDustThresholdUpdate

```solidity
function finalizeDepositDustThresholdUpdate(struct BridgeGovernanceParameters.DepositData self, uint256 governanceDelay) external
```

Finalizes the deposit dust threshold amount update process.

Can be called after the governance delay elapses.

### beginDepositTreasuryFeeDivisorUpdate

```solidity
function beginDepositTreasuryFeeDivisorUpdate(struct BridgeGovernanceParameters.DepositData self, uint64 _newDepositTreasuryFeeDivisor) external
```

Begins the deposit treasury fee divisor amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.DepositData |  |
| _newDepositTreasuryFeeDivisor | uint64 | New deposit treasury fee divisor amount. |

### finalizeDepositTreasuryFeeDivisorUpdate

```solidity
function finalizeDepositTreasuryFeeDivisorUpdate(struct BridgeGovernanceParameters.DepositData self, uint256 governanceDelay) external
```

Finalizes the deposit treasury fee divisor amount update process.

Can be called after the governance delay elapses.

### beginDepositTxMaxFeeUpdate

```solidity
function beginDepositTxMaxFeeUpdate(struct BridgeGovernanceParameters.DepositData self, uint64 _newDepositTxMaxFee) external
```

Begins the deposit tx max fee amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.DepositData |  |
| _newDepositTxMaxFee | uint64 | New deposit tx max fee amount. |

### finalizeDepositTxMaxFeeUpdate

```solidity
function finalizeDepositTxMaxFeeUpdate(struct BridgeGovernanceParameters.DepositData self, uint256 governanceDelay) external
```

Finalizes the deposit tx max fee amount update process.

Can be called after the governance delay elapses.

### beginDepositRevealAheadPeriodUpdate

```solidity
function beginDepositRevealAheadPeriodUpdate(struct BridgeGovernanceParameters.DepositData self, uint32 _newDepositRevealAheadPeriod) external
```

Begins the deposit reveal ahead period update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.DepositData |  |
| _newDepositRevealAheadPeriod | uint32 | New deposit reveal ahead period. |

### finalizeDepositRevealAheadPeriodUpdate

```solidity
function finalizeDepositRevealAheadPeriodUpdate(struct BridgeGovernanceParameters.DepositData self, uint256 governanceDelay) external
```

Finalizes the deposit reveal ahead period update process.

Can be called after the governance delay elapses.

### beginRedemptionDustThresholdUpdate

```solidity
function beginRedemptionDustThresholdUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint64 _newRedemptionDustThreshold) external
```

Begins the redemption dust threshold amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.RedemptionData |  |
| _newRedemptionDustThreshold | uint64 | New redemption dust threshold amount. |

### finalizeRedemptionDustThresholdUpdate

```solidity
function finalizeRedemptionDustThresholdUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint256 governanceDelay) external
```

Finalizes the redemption dust threshold amount update process.

Can be called after the governance delay elapses.

### beginRedemptionTreasuryFeeDivisorUpdate

```solidity
function beginRedemptionTreasuryFeeDivisorUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint64 _newRedemptionTreasuryFeeDivisor) external
```

Begins the redemption treasury fee divisor amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.RedemptionData |  |
| _newRedemptionTreasuryFeeDivisor | uint64 | New redemption treasury fee divisor amount. |

### finalizeRedemptionTreasuryFeeDivisorUpdate

```solidity
function finalizeRedemptionTreasuryFeeDivisorUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint256 governanceDelay) external
```

Finalizes the redemption treasury fee divisor amount update process.

Can be called after the governance delay elapses.

### beginRedemptionTxMaxFeeUpdate

```solidity
function beginRedemptionTxMaxFeeUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint64 _newRedemptionTxMaxFee) external
```

Begins the redemption tx max fee amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.RedemptionData |  |
| _newRedemptionTxMaxFee | uint64 | New redemption tx max fee amount. |

### finalizeRedemptionTxMaxFeeUpdate

```solidity
function finalizeRedemptionTxMaxFeeUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint256 governanceDelay) external
```

Finalizes the redemption tx max fee amount update process.

Can be called after the governance delay elapses.

### beginRedemptionTxMaxTotalFeeUpdate

```solidity
function beginRedemptionTxMaxTotalFeeUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint64 _newRedemptionTxMaxTotalFee) external
```

Begins the redemption tx max total fee amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.RedemptionData |  |
| _newRedemptionTxMaxTotalFee | uint64 | New redemption tx max total fee amount. |

### finalizeRedemptionTxMaxTotalFeeUpdate

```solidity
function finalizeRedemptionTxMaxTotalFeeUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint256 governanceDelay) external
```

Finalizes the redemption tx max total fee amount update process.

Can be called after the governance delay elapses.

### beginRedemptionTimeoutUpdate

```solidity
function beginRedemptionTimeoutUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint32 _newRedemptionTimeout) external
```

Begins the redemption timeout amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.RedemptionData |  |
| _newRedemptionTimeout | uint32 | New redemption timeout amount. |

### finalizeRedemptionTimeoutUpdate

```solidity
function finalizeRedemptionTimeoutUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint256 governanceDelay) external
```

Finalizes the redemption timeout amount update
process.

Can be called after the governance delay elapses.

### beginRedemptionTimeoutSlashingAmountUpdate

```solidity
function beginRedemptionTimeoutSlashingAmountUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint96 _newRedemptionTimeoutSlashingAmount) external
```

Begins the redemption timeout slashing amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.RedemptionData |  |
| _newRedemptionTimeoutSlashingAmount | uint96 | New redemption timeout slashing amount. |

### finalizeRedemptionTimeoutSlashingAmountUpdate

```solidity
function finalizeRedemptionTimeoutSlashingAmountUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint256 governanceDelay) external
```

Finalizes the redemption timeout slashing amount update process.

Can be called after the governance delay elapses.

### beginRedemptionTimeoutNotifierRewardMultiplierUpdate

```solidity
function beginRedemptionTimeoutNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint32 _newRedemptionTimeoutNotifierRewardMultiplier) external
```

Begins the redemption timeout notifier reward multiplier amount
update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.RedemptionData |  |
| _newRedemptionTimeoutNotifierRewardMultiplier | uint32 | New redemption timeout notifier reward multiplier amount. |

### finalizeRedemptionTimeoutNotifierRewardMultiplierUpdate

```solidity
function finalizeRedemptionTimeoutNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.RedemptionData self, uint256 governanceDelay) external
```

Finalizes the redemption timeout notifier reward multiplier amount update process.

Can be called after the governance delay elapses.

### beginMovingFundsTxMaxTotalFeeUpdate

```solidity
function beginMovingFundsTxMaxTotalFeeUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint64 _newMovingFundsTxMaxTotalFee) external
```

Begins the moving funds tx max total fee amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovingFundsTxMaxTotalFee | uint64 | New moving funds tx max total fee amount. |

### finalizeMovingFundsTxMaxTotalFeeUpdate

```solidity
function finalizeMovingFundsTxMaxTotalFeeUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moving funds tx max total fee amount update process.

Can be called after the governance delay elapses.

### beginMovingFundsDustThresholdUpdate

```solidity
function beginMovingFundsDustThresholdUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint64 _newMovingFundsDustThreshold) external
```

Begins the moving funds dust threshold amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovingFundsDustThreshold | uint64 | New moving funds dust threshold amount. |

### finalizeMovingFundsDustThresholdUpdate

```solidity
function finalizeMovingFundsDustThresholdUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moving funds dust threshold amount update process.

Can be called after the governance delay elapses.

### beginMovingFundsTimeoutResetDelayUpdate

```solidity
function beginMovingFundsTimeoutResetDelayUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint32 _newMovingFundsTimeoutResetDelay) external
```

Begins the moving funds timeout reset delay amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovingFundsTimeoutResetDelay | uint32 | New moving funds timeout reset delay amount. |

### finalizeMovingFundsTimeoutResetDelayUpdate

```solidity
function finalizeMovingFundsTimeoutResetDelayUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moving funds timeout reset delay amount update process.

Can be called after the governance delay elapses.

### beginMovingFundsTimeoutUpdate

```solidity
function beginMovingFundsTimeoutUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint32 _newMovingFundsTimeout) external
```

Begins the moving funds timeout amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovingFundsTimeout | uint32 | New moving funds timeout amount. |

### finalizeMovingFundsTimeoutUpdate

```solidity
function finalizeMovingFundsTimeoutUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moving funds timeout amount update process.

Can be called after the governance delay elapses.

### beginMovingFundsTimeoutSlashingAmountUpdate

```solidity
function beginMovingFundsTimeoutSlashingAmountUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint96 _newMovingFundsTimeoutSlashingAmount) external
```

Begins the moving funds timeout slashing amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovingFundsTimeoutSlashingAmount | uint96 | New moving funds timeout slashing amount. |

### finalizeMovingFundsTimeoutSlashingAmountUpdate

```solidity
function finalizeMovingFundsTimeoutSlashingAmountUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moving funds timeout slashing amount update process.

Can be called after the governance delay elapses.

### beginMovingFundsTimeoutNotifierRewardMultiplierUpdate

```solidity
function beginMovingFundsTimeoutNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint32 _newMovingFundsTimeoutNotifierRewardMultiplier) external
```

Begins the moving funds timeout notifier reward multiplier amount
update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovingFundsTimeoutNotifierRewardMultiplier | uint32 | New moving funds timeout notifier reward multiplier amount. |

### finalizeMovingFundsTimeoutNotifierRewardMultiplierUpdate

```solidity
function finalizeMovingFundsTimeoutNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moving funds timeout notifier reward multiplier
amount update process.

Can be called after the governance delay elapses.

### beginMovingFundsCommitmentGasOffsetUpdate

```solidity
function beginMovingFundsCommitmentGasOffsetUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint16 _newMovingFundsCommitmentGasOffset) external
```

Begins the moving funds commitment gas offset update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovingFundsCommitmentGasOffset | uint16 | New moving funds commitment gas offset. |

### finalizeMovingFundsCommitmentGasOffsetUpdate

```solidity
function finalizeMovingFundsCommitmentGasOffsetUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moving funds commitment gas offset update process.

Can be called after the governance delay elapses.

### beginMovedFundsSweepTxMaxTotalFeeUpdate

```solidity
function beginMovedFundsSweepTxMaxTotalFeeUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint64 _newMovedFundsSweepTxMaxTotalFee) external
```

Begins the moved funds sweep tx max total fee amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovedFundsSweepTxMaxTotalFee | uint64 | New moved funds sweep tx max total fee amount. |

### finalizeMovedFundsSweepTxMaxTotalFeeUpdate

```solidity
function finalizeMovedFundsSweepTxMaxTotalFeeUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moved funds sweep tx max total fee amount update
process.

Can be called after the governance delay elapses.

### beginMovedFundsSweepTimeoutUpdate

```solidity
function beginMovedFundsSweepTimeoutUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint32 _newMovedFundsSweepTimeout) external
```

Begins the moved funds sweep timeout amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovedFundsSweepTimeout | uint32 | New moved funds sweep timeout amount. |

### finalizeMovedFundsSweepTimeoutUpdate

```solidity
function finalizeMovedFundsSweepTimeoutUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moved funds sweep timeout amount update process.

Can be called after the governance delay elapses.

### beginMovedFundsSweepTimeoutSlashingAmountUpdate

```solidity
function beginMovedFundsSweepTimeoutSlashingAmountUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint96 _newMovedFundsSweepTimeoutSlashingAmount) external
```

Begins the moved funds sweep timeout slashing amount update
process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovedFundsSweepTimeoutSlashingAmount | uint96 | New moved funds sweep timeout slashing amount. |

### finalizeMovedFundsSweepTimeoutSlashingAmountUpdate

```solidity
function finalizeMovedFundsSweepTimeoutSlashingAmountUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moved funds sweep timeout slashing amount
update process.

Can be called after the governance delay elapses.

### beginMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate

```solidity
function beginMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint32 _newMovedFundsSweepTimeoutNotifierRewardMultiplier) external
```

Begins the moved funds sweep timeout notifier reward multiplier
amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.MovingFundsData |  |
| _newMovedFundsSweepTimeoutNotifierRewardMultiplier | uint32 | New moved funds sweep timeout notifier reward multiplier amount. |

### finalizeMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate

```solidity
function finalizeMovedFundsSweepTimeoutNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.MovingFundsData self, uint256 governanceDelay) external
```

Finalizes the moved funds sweep timeout notifier reward multiplier
amount update process.

Can be called after the governance delay elapses.

### beginWalletCreationPeriodUpdate

```solidity
function beginWalletCreationPeriodUpdate(struct BridgeGovernanceParameters.WalletData self, uint32 _newWalletCreationPeriod) external
```

Begins the wallet creation period amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.WalletData |  |
| _newWalletCreationPeriod | uint32 | New wallet creation period amount. |

### finalizeWalletCreationPeriodUpdate

```solidity
function finalizeWalletCreationPeriodUpdate(struct BridgeGovernanceParameters.WalletData self, uint256 governanceDelay) external
```

Finalizes the wallet creation period amount update process.

Can be called after the governance delay elapses.

### beginWalletCreationMinBtcBalanceUpdate

```solidity
function beginWalletCreationMinBtcBalanceUpdate(struct BridgeGovernanceParameters.WalletData self, uint64 _newWalletCreationMinBtcBalance) external
```

Begins the wallet creation min btc balance amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.WalletData |  |
| _newWalletCreationMinBtcBalance | uint64 | New wallet creation min btc balance amount. |

### finalizeWalletCreationMinBtcBalanceUpdate

```solidity
function finalizeWalletCreationMinBtcBalanceUpdate(struct BridgeGovernanceParameters.WalletData self, uint256 governanceDelay) external
```

Finalizes the wallet creation min btc balance amount update process.

Can be called after the governance delay elapses.

### beginWalletCreationMaxBtcBalanceUpdate

```solidity
function beginWalletCreationMaxBtcBalanceUpdate(struct BridgeGovernanceParameters.WalletData self, uint64 _newWalletCreationMaxBtcBalance) external
```

Begins the wallet creation max btc balance amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.WalletData |  |
| _newWalletCreationMaxBtcBalance | uint64 | New wallet creation max btc balance amount. |

### finalizeWalletCreationMaxBtcBalanceUpdate

```solidity
function finalizeWalletCreationMaxBtcBalanceUpdate(struct BridgeGovernanceParameters.WalletData self, uint256 governanceDelay) external
```

Finalizes the wallet creation max btc balance amount update process.

Can be called after the governance delay elapses.

### beginWalletClosureMinBtcBalanceUpdate

```solidity
function beginWalletClosureMinBtcBalanceUpdate(struct BridgeGovernanceParameters.WalletData self, uint64 _newWalletClosureMinBtcBalance) external
```

Begins the wallet closure min btc balance amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.WalletData |  |
| _newWalletClosureMinBtcBalance | uint64 | New wallet closure min btc balance amount. |

### finalizeWalletClosureMinBtcBalanceUpdate

```solidity
function finalizeWalletClosureMinBtcBalanceUpdate(struct BridgeGovernanceParameters.WalletData self, uint256 governanceDelay) external
```

Finalizes the wallet closure min btc balance amount update process.

Can be called after the governance delay elapses.

### beginWalletMaxAgeUpdate

```solidity
function beginWalletMaxAgeUpdate(struct BridgeGovernanceParameters.WalletData self, uint32 _newWalletMaxAge) external
```

Begins the wallet max age amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.WalletData |  |
| _newWalletMaxAge | uint32 | New wallet max age amount. |

### finalizeWalletMaxAgeUpdate

```solidity
function finalizeWalletMaxAgeUpdate(struct BridgeGovernanceParameters.WalletData self, uint256 governanceDelay) external
```

Finalizes the wallet max age amount update process.

Can be called after the governance delay elapses.

### beginWalletMaxBtcTransferUpdate

```solidity
function beginWalletMaxBtcTransferUpdate(struct BridgeGovernanceParameters.WalletData self, uint64 _newWalletMaxBtcTransfer) external
```

Begins the wallet max btc transfer amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.WalletData |  |
| _newWalletMaxBtcTransfer | uint64 | New wallet max btc transfer amount. |

### finalizeWalletMaxBtcTransferUpdate

```solidity
function finalizeWalletMaxBtcTransferUpdate(struct BridgeGovernanceParameters.WalletData self, uint256 governanceDelay) external
```

Finalizes the wallet max btc transfer amount update process.

Can be called after the governance delay elapses.

### beginWalletClosingPeriodUpdate

```solidity
function beginWalletClosingPeriodUpdate(struct BridgeGovernanceParameters.WalletData self, uint32 _newWalletClosingPeriod) external
```

Begins the wallet closing period amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.WalletData |  |
| _newWalletClosingPeriod | uint32 | New wallet closing period amount. |

### finalizeWalletClosingPeriodUpdate

```solidity
function finalizeWalletClosingPeriodUpdate(struct BridgeGovernanceParameters.WalletData self, uint256 governanceDelay) external
```

Finalizes the wallet closing period amount update process.

Can be called after the governance delay elapses.

### beginFraudChallengeDepositAmountUpdate

```solidity
function beginFraudChallengeDepositAmountUpdate(struct BridgeGovernanceParameters.FraudData self, uint96 _newFraudChallengeDepositAmount) external
```

Begins the fraud challenge deposit amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.FraudData |  |
| _newFraudChallengeDepositAmount | uint96 | New fraud challenge deposit amount. |

### finalizeFraudChallengeDepositAmountUpdate

```solidity
function finalizeFraudChallengeDepositAmountUpdate(struct BridgeGovernanceParameters.FraudData self, uint256 governanceDelay) external
```

Finalizes the fraud challenge deposit amount update process.

Can be called after the governance delay elapses.

### beginFraudChallengeDefeatTimeoutUpdate

```solidity
function beginFraudChallengeDefeatTimeoutUpdate(struct BridgeGovernanceParameters.FraudData self, uint32 _newFraudChallengeDefeatTimeout) external
```

Begins the fraud challenge defeat timeout amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.FraudData |  |
| _newFraudChallengeDefeatTimeout | uint32 | New fraud challenge defeat timeout amount. |

### finalizeFraudChallengeDefeatTimeoutUpdate

```solidity
function finalizeFraudChallengeDefeatTimeoutUpdate(struct BridgeGovernanceParameters.FraudData self, uint256 governanceDelay) external
```

Finalizes the fraud challenge defeat timeout amount update process.

Can be called after the governance delay elapses.

### beginFraudSlashingAmountUpdate

```solidity
function beginFraudSlashingAmountUpdate(struct BridgeGovernanceParameters.FraudData self, uint96 _newFraudSlashingAmount) external
```

Begins the fraud slashing amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.FraudData |  |
| _newFraudSlashingAmount | uint96 | New fraud slashing amount. |

### finalizeFraudSlashingAmountUpdate

```solidity
function finalizeFraudSlashingAmountUpdate(struct BridgeGovernanceParameters.FraudData self, uint256 governanceDelay) external
```

Finalizes the fraud slashing amount update process.

Can be called after the governance delay elapses.

### beginFraudNotifierRewardMultiplierUpdate

```solidity
function beginFraudNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.FraudData self, uint32 _newFraudNotifierRewardMultiplier) external
```

Begins the fraud notifier reward multiplier amount update process.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.FraudData |  |
| _newFraudNotifierRewardMultiplier | uint32 | New fraud notifier reward multiplier amount. |

### finalizeFraudNotifierRewardMultiplierUpdate

```solidity
function finalizeFraudNotifierRewardMultiplierUpdate(struct BridgeGovernanceParameters.FraudData self, uint256 governanceDelay) external
```

Finalizes the fraud notifier reward multiplier amount update process.

Can be called after the governance delay elapses.

### beginTreasuryUpdate

```solidity
function beginTreasuryUpdate(struct BridgeGovernanceParameters.TreasuryData self, address _newTreasury) external
```

Begins the treasury address update process.

It does not perform any parameter validation.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeGovernanceParameters.TreasuryData |  |
| _newTreasury | address | New treasury address. |

### finalizeTreasuryUpdate

```solidity
function finalizeTreasuryUpdate(struct BridgeGovernanceParameters.TreasuryData self, uint256 governanceDelay) external
```

Finalizes the treasury address update process.

Can be called after the governance delay elapses.

## BridgeState

### Storage

```solidity
struct Storage {
  contract Bank bank;
  contract IRelay relay;
  uint96 txProofDifficultyFactor;
  contract IWalletRegistry ecdsaWalletRegistry;
  contract ReimbursementPool reimbursementPool;
  address treasury;
  bytes32 __treasuryAlignmentGap;
  uint64 depositDustThreshold;
  uint64 depositTreasuryFeeDivisor;
  uint64 depositTxMaxFee;
  uint32 depositRevealAheadPeriod;
  bytes32 __depositAlignmentGap;
  uint64 movingFundsTxMaxTotalFee;
  uint64 movingFundsDustThreshold;
  uint32 movingFundsTimeoutResetDelay;
  uint32 movingFundsTimeout;
  uint96 movingFundsTimeoutSlashingAmount;
  uint32 movingFundsTimeoutNotifierRewardMultiplier;
  uint16 movingFundsCommitmentGasOffset;
  bytes32 __movingFundsAlignmentGap;
  uint64 movedFundsSweepTxMaxTotalFee;
  uint32 movedFundsSweepTimeout;
  uint96 movedFundsSweepTimeoutSlashingAmount;
  uint32 movedFundsSweepTimeoutNotifierRewardMultiplier;
  uint64 redemptionDustThreshold;
  uint64 redemptionTreasuryFeeDivisor;
  uint64 redemptionTxMaxFee;
  uint64 redemptionTxMaxTotalFee;
  bytes32 __redemptionAlignmentGap;
  uint32 redemptionTimeout;
  uint96 redemptionTimeoutSlashingAmount;
  uint32 redemptionTimeoutNotifierRewardMultiplier;
  uint96 fraudChallengeDepositAmount;
  uint32 fraudChallengeDefeatTimeout;
  uint96 fraudSlashingAmount;
  uint32 fraudNotifierRewardMultiplier;
  uint32 walletCreationPeriod;
  uint64 walletCreationMinBtcBalance;
  uint64 walletCreationMaxBtcBalance;
  uint64 walletClosureMinBtcBalance;
  uint32 walletMaxAge;
  bytes20 activeWalletPubKeyHash;
  uint32 liveWalletsCount;
  uint64 walletMaxBtcTransfer;
  uint32 walletClosingPeriod;
  mapping(uint256 => struct Deposit.DepositRequest) deposits;
  mapping(address => bool) isVaultTrusted;
  mapping(address => bool) isSpvMaintainer;
  mapping(uint256 => struct MovingFunds.MovedFundsSweepRequest) movedFundsSweepRequests;
  mapping(uint256 => struct Redemption.RedemptionRequest) pendingRedemptions;
  mapping(uint256 => struct Redemption.RedemptionRequest) timedOutRedemptions;
  mapping(uint256 => struct Fraud.FraudChallenge) fraudChallenges;
  mapping(uint256 => bool) spentMainUTXOs;
  mapping(bytes20 => struct Wallets.Wallet) registeredWallets;
  uint256[50] __gap;
}
```

### DepositParametersUpdated

```solidity
event DepositParametersUpdated(uint64 depositDustThreshold, uint64 depositTreasuryFeeDivisor, uint64 depositTxMaxFee, uint32 depositRevealAheadPeriod)
```

### RedemptionParametersUpdated

```solidity
event RedemptionParametersUpdated(uint64 redemptionDustThreshold, uint64 redemptionTreasuryFeeDivisor, uint64 redemptionTxMaxFee, uint64 redemptionTxMaxTotalFee, uint32 redemptionTimeout, uint96 redemptionTimeoutSlashingAmount, uint32 redemptionTimeoutNotifierRewardMultiplier)
```

### MovingFundsParametersUpdated

```solidity
event MovingFundsParametersUpdated(uint64 movingFundsTxMaxTotalFee, uint64 movingFundsDustThreshold, uint32 movingFundsTimeoutResetDelay, uint32 movingFundsTimeout, uint96 movingFundsTimeoutSlashingAmount, uint32 movingFundsTimeoutNotifierRewardMultiplier, uint16 movingFundsCommitmentGasOffset, uint64 movedFundsSweepTxMaxTotalFee, uint32 movedFundsSweepTimeout, uint96 movedFundsSweepTimeoutSlashingAmount, uint32 movedFundsSweepTimeoutNotifierRewardMultiplier)
```

### WalletParametersUpdated

```solidity
event WalletParametersUpdated(uint32 walletCreationPeriod, uint64 walletCreationMinBtcBalance, uint64 walletCreationMaxBtcBalance, uint64 walletClosureMinBtcBalance, uint32 walletMaxAge, uint64 walletMaxBtcTransfer, uint32 walletClosingPeriod)
```

### FraudParametersUpdated

```solidity
event FraudParametersUpdated(uint96 fraudChallengeDepositAmount, uint32 fraudChallengeDefeatTimeout, uint96 fraudSlashingAmount, uint32 fraudNotifierRewardMultiplier)
```

### TreasuryUpdated

```solidity
event TreasuryUpdated(address treasury)
```

### updateDepositParameters

```solidity
function updateDepositParameters(struct BridgeState.Storage self, uint64 _depositDustThreshold, uint64 _depositTreasuryFeeDivisor, uint64 _depositTxMaxFee, uint32 _depositRevealAheadPeriod) internal
```

of `depositTreasuryFeeDivisor` and `depositTxMaxFee` parameters
in order to make requests that can incur the treasury and
transaction fee and still satisfy the depositor.

Requirements:
- Deposit dust threshold must be greater than zero,
- Deposit dust threshold must be greater than deposit TX max fee,
- Deposit transaction max fee must be greater than zero.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| _depositDustThreshold | uint64 |  |
| _depositTreasuryFeeDivisor | uint64 | New value of the treasury fee divisor. It is the divisor used to compute the treasury fee taken from each deposit and transferred to the treasury upon sweep proof submission. That fee is computed as follows: `treasuryFee = depositedAmount / depositTreasuryFeeDivisor` For example, if the treasury fee needs to be 2% of each deposit, the `depositTreasuryFeeDivisor` should be set to `50` because `1/50 = 0.02 = 2%`. |
| _depositTxMaxFee | uint64 | New value of the deposit tx max fee in satoshis. It is the maximum amount of BTC transaction fee that can be incurred by each swept deposit being part of the given sweep transaction. If the maximum BTC transaction fee is exceeded, such transaction is considered a fraud. |
| _depositRevealAheadPeriod | uint32 | New value of the deposit reveal ahead period parameter in seconds. It defines the length of the period that must be preserved between the deposit reveal time and the deposit refund locktime. |

### updateRedemptionParameters

```solidity
function updateRedemptionParameters(struct BridgeState.Storage self, uint64 _redemptionDustThreshold, uint64 _redemptionTreasuryFeeDivisor, uint64 _redemptionTxMaxFee, uint64 _redemptionTxMaxTotalFee, uint32 _redemptionTimeout, uint96 _redemptionTimeoutSlashingAmount, uint32 _redemptionTimeoutNotifierRewardMultiplier) internal
```

Updates parameters of redemptions.

Requirements:
- Redemption dust threshold must be greater than moving funds dust
threshold,
- Redemption dust threshold must be greater than the redemption TX
max fee,
- Redemption transaction max fee must be greater than zero,
- Redemption transaction max total fee must be greater than or
equal to the redemption transaction per-request max fee,
- Redemption timeout must be greater than zero,
- Redemption timeout notifier reward multiplier must be in the
range [0, 100].

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| _redemptionDustThreshold | uint64 | New value of the redemption dust threshold in satoshis. It is the minimal amount that can be requested for redemption. Value of this parameter must take into account the value of `redemptionTreasuryFeeDivisor` and `redemptionTxMaxFee` parameters in order to make requests that can incur the treasury and transaction fee and still satisfy the redeemer. |
| _redemptionTreasuryFeeDivisor | uint64 | New value of the redemption treasury fee divisor. It is the divisor used to compute the treasury fee taken from each redemption request and transferred to the treasury upon successful request finalization. That fee is computed as follows: `treasuryFee = requestedAmount / redemptionTreasuryFeeDivisor` For example, if the treasury fee needs to be 2% of each redemption request, the `redemptionTreasuryFeeDivisor` should be set to `50` because `1/50 = 0.02 = 2%`. |
| _redemptionTxMaxFee | uint64 | New value of the redemption transaction max fee in satoshis. It is the maximum amount of BTC transaction fee that can be incurred by each redemption request being part of the given redemption transaction. If the maximum BTC transaction fee is exceeded, such transaction is considered a fraud. This is a per-redemption output max fee for the redemption transaction. |
| _redemptionTxMaxTotalFee | uint64 | New value of the redemption transaction max total fee in satoshis. It is the maximum amount of the total BTC transaction fee that is acceptable in a single redemption transaction. This is a _total_ max fee for the entire redemption transaction. |
| _redemptionTimeout | uint32 | New value of the redemption timeout in seconds. It is the time after which the redemption request can be reported as timed out. It is counted from the moment when the redemption request was created via `requestRedemption` call. Reported  timed out requests are cancelled and locked TBTC is returned to the redeemer in full amount. |
| _redemptionTimeoutSlashingAmount | uint96 | New value of the redemption timeout slashing amount in T, it is the amount slashed from each wallet member for redemption timeout. |
| _redemptionTimeoutNotifierRewardMultiplier | uint32 | New value of the redemption timeout notifier reward multiplier as percentage, it determines the percentage of the notifier reward from the staking contact the notifier of a redemption timeout receives. The value must be in the range [0, 100]. |

### updateMovingFundsParameters

```solidity
function updateMovingFundsParameters(struct BridgeState.Storage self, uint64 _movingFundsTxMaxTotalFee, uint64 _movingFundsDustThreshold, uint32 _movingFundsTimeoutResetDelay, uint32 _movingFundsTimeout, uint96 _movingFundsTimeoutSlashingAmount, uint32 _movingFundsTimeoutNotifierRewardMultiplier, uint16 _movingFundsCommitmentGasOffset, uint64 _movedFundsSweepTxMaxTotalFee, uint32 _movedFundsSweepTimeout, uint96 _movedFundsSweepTimeoutSlashingAmount, uint32 _movedFundsSweepTimeoutNotifierRewardMultiplier) internal
```

Updates parameters of moving funds.

Requirements:
- Moving funds transaction max total fee must be greater than zero,
- Moving funds dust threshold must be greater than zero and lower
than the redemption dust threshold,
- Moving funds timeout reset delay must be greater than zero,
- Moving funds timeout must be greater than the moving funds
timeout reset delay,
- Moving funds timeout notifier reward multiplier must be in the
range [0, 100],
- Moved funds sweep transaction max total fee must be greater than zero,
- Moved funds sweep timeout must be greater than zero,
- Moved funds sweep timeout notifier reward multiplier must be in the
range [0, 100].

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| _movingFundsTxMaxTotalFee | uint64 | New value of the moving funds transaction max total fee in satoshis. It is the maximum amount of the total BTC transaction fee that is acceptable in a single moving funds transaction. This is a _total_ max fee for the entire moving funds transaction. |
| _movingFundsDustThreshold | uint64 | New value of the moving funds dust threshold. It is the minimal satoshi amount that makes sense to be transferred during the moving funds process. Moving funds wallets having their BTC balance below that value can begin closing immediately as transferring such a low value may not be possible due to BTC network fees. |
| _movingFundsTimeoutResetDelay | uint32 | New value of the moving funds timeout reset delay in seconds. It is the time after which the moving funds timeout can be reset in case the target wallet commitment cannot be submitted due to a lack of live wallets in the system. It is counted from the moment when the wallet was requested to move their funds and switched to the MovingFunds state or from the moment the timeout was reset the last time. |
| _movingFundsTimeout | uint32 | New value of the moving funds timeout in seconds. It is the time after which the moving funds process can be reported as timed out. It is counted from the moment when the wallet was requested to move their funds and switched to the MovingFunds state. |
| _movingFundsTimeoutSlashingAmount | uint96 | New value of the moving funds timeout slashing amount in T, it is the amount slashed from each wallet member for moving funds timeout. |
| _movingFundsTimeoutNotifierRewardMultiplier | uint32 | New value of the moving funds timeout notifier reward multiplier as percentage, it determines the percentage of the notifier reward from the staking contact the notifier of a moving funds timeout receives. The value must be in the range [0, 100]. |
| _movingFundsCommitmentGasOffset | uint16 | New value of the gas offset for moving funds target wallet commitment transaction gas costs reimbursement. |
| _movedFundsSweepTxMaxTotalFee | uint64 | New value of the moved funds sweep transaction max total fee in satoshis. It is the maximum amount of the total BTC transaction fee that is acceptable in a single moved funds sweep transaction. This is a _total_ max fee for the entire moved funds sweep transaction. |
| _movedFundsSweepTimeout | uint32 | New value of the moved funds sweep timeout in seconds. It is the time after which the moved funds sweep process can be reported as timed out. It is counted from the moment when the wallet was requested to sweep the received funds. |
| _movedFundsSweepTimeoutSlashingAmount | uint96 | New value of the moved funds sweep timeout slashing amount in T, it is the amount slashed from each wallet member for moved funds sweep timeout. |
| _movedFundsSweepTimeoutNotifierRewardMultiplier | uint32 | New value of the moved funds sweep timeout notifier reward multiplier as percentage, it determines the percentage of the notifier reward from the staking contact the notifier of a moved funds sweep timeout receives. The value must be in the range [0, 100]. |

### updateWalletParameters

```solidity
function updateWalletParameters(struct BridgeState.Storage self, uint32 _walletCreationPeriod, uint64 _walletCreationMinBtcBalance, uint64 _walletCreationMaxBtcBalance, uint64 _walletClosureMinBtcBalance, uint32 _walletMaxAge, uint64 _walletMaxBtcTransfer, uint32 _walletClosingPeriod) internal
```

Requirements:
- Wallet maximum BTC balance must be greater than the wallet
minimum BTC balance,
- Wallet maximum BTC transfer must be greater than zero,
- Wallet closing period must be greater than zero.

### updateFraudParameters

```solidity
function updateFraudParameters(struct BridgeState.Storage self, uint96 _fraudChallengeDepositAmount, uint32 _fraudChallengeDefeatTimeout, uint96 _fraudSlashingAmount, uint32 _fraudNotifierRewardMultiplier) internal
```

Updates parameters related to frauds.

Requirements:
- Fraud challenge defeat timeout must be greater than 0,
- Fraud notifier reward multiplier must be in the range [0, 100].

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| _fraudChallengeDepositAmount | uint96 | New value of the fraud challenge deposit amount in wei, it is the amount of ETH the party challenging the wallet for fraud needs to deposit. |
| _fraudChallengeDefeatTimeout | uint32 | New value of the challenge defeat timeout in seconds, it is the amount of time the wallet has to defeat a fraud challenge. The value must be greater than zero. |
| _fraudSlashingAmount | uint96 | New value of the fraud slashing amount in T, it is the amount slashed from each wallet member for committing a fraud. |
| _fraudNotifierRewardMultiplier | uint32 | New value of the fraud notifier reward multiplier as percentage, it determines the percentage of the notifier reward from the staking contact the notifier of a fraud receives. The value must be in the range [0, 100]. |

### updateTreasury

```solidity
function updateTreasury(struct BridgeState.Storage self, address _treasury) internal
```

Updates treasury address. The treasury receives the system fees.

The treasury address must not be 0x0.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| _treasury | address | New value of the treasury address. |

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

## EcdsaLib

### compressPublicKey

```solidity
function compressPublicKey(bytes32 x, bytes32 y) internal pure returns (bytes)
```

Converts public key X and Y coordinates (32-byte each) to a
compressed public key (33-byte). Compressed public key is X
coordinate prefixed with `02` or `03` based on the Y coordinate parity.
It is expected that the uncompressed public key is stripped
(i.e. it is not prefixed with `04`).

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| x | bytes32 | Wallet's public key's X coordinate. |
| y | bytes32 | Wallet's public key's Y coordinate. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bytes | Compressed public key (33-byte), prefixed with `02` or `03`. |

## Fraud

The library handles the logic for challenging Bridge wallets that
committed fraud.

Anyone can submit a fraud challenge indicating that a UTXO being under
the wallet control was unlocked by the wallet but was not used
according to the protocol rules. That means the wallet signed
a transaction input pointing to that UTXO and there is a unique
sighash and signature pair associated with that input.

In order to defeat the challenge, the same wallet public key and
signature must be provided as were used to calculate the sighash during
the challenge. The wallet provides the preimage which produces sighash
used to generate the ECDSA signature that is the subject of the fraud
claim.

The fraud challenge defeat attempt will succeed if the inputs in the
preimage are considered honestly spent by the wallet. Therefore the
transaction spending the UTXO must be proven in the Bridge before
a challenge defeat is called.

Another option is when a malicious wallet member used a signed heartbeat
message periodically produced by the wallet off-chain to challenge the
wallet for a fraud. Anyone from the wallet can defeat the challenge by
proving the sighash and signature were produced for a heartbeat message
following a strict format.

### FraudChallenge

```solidity
struct FraudChallenge {
  address challenger;
  uint256 depositAmount;
  uint32 reportedAt;
  bool resolved;
}
```

### FraudChallengeSubmitted

```solidity
event FraudChallengeSubmitted(bytes20 walletPubKeyHash, bytes32 sighash, uint8 v, bytes32 r, bytes32 s)
```

### FraudChallengeDefeated

```solidity
event FraudChallengeDefeated(bytes20 walletPubKeyHash, bytes32 sighash)
```

### FraudChallengeDefeatTimedOut

```solidity
event FraudChallengeDefeatTimedOut(bytes20 walletPubKeyHash, bytes32 sighash)
```

### submitFraudChallenge

```solidity
function submitFraudChallenge(struct BridgeState.Storage self, bytes walletPublicKey, bytes preimageSha256, struct BitcoinTx.RSVSignature signature) external
```

Submits a fraud challenge indicating that a UTXO being under
wallet control was unlocked by the wallet but was not used
according to the protocol rules. That means the wallet signed
a transaction input pointing to that UTXO and there is a unique
sighash and signature pair associated with that input. This
function uses those parameters to create a fraud accusation that
proves a given transaction input unlocking the given UTXO was
actually signed by the wallet. This function cannot determine
whether the transaction was actually broadcast and the input was
consumed in a fraudulent way so it just opens a challenge period
during which the wallet can defeat the challenge by submitting
proof of a transaction that consumes the given input according
to protocol rules. To prevent spurious allegations, the caller
must deposit ETH that is returned back upon justified fraud
challenge or confiscated otherwise.

Requirements:
- Wallet behind `walletPublicKey` must be in Live or MovingFunds
or Closing state,
- The challenger must send appropriate amount of ETH used as
fraud challenge deposit,
- The signature (represented by r, s and v) must be generated by
the wallet behind `walletPubKey` during signing of `sighash`
which was calculated from `preimageSha256`,
- Wallet can be challenged for the given signature only once.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPublicKey | bytes | The public key of the wallet in the uncompressed and unprefixed format (64 bytes). |
| preimageSha256 | bytes | The hash that was generated by applying SHA-256 one time over the preimage used during input signing. The preimage is a serialized subset of the transaction and its structure depends on the transaction input (see BIP-143 for reference). Notice that applying SHA-256 over the `preimageSha256` results in `sighash`.  The path from `preimage` to `sighash` looks like this: preimage -> (SHA-256) -> preimageSha256 -> (SHA-256) -> sighash. |
| signature | struct BitcoinTx.RSVSignature | Bitcoin signature in the R/S/V format |

### defeatFraudChallenge

```solidity
function defeatFraudChallenge(struct BridgeState.Storage self, bytes walletPublicKey, bytes preimage, bool witness) external
```

Allows to defeat a pending fraud challenge against a wallet if
the transaction that spends the UTXO follows the protocol rules.
In order to defeat the challenge the same `walletPublicKey` and
signature (represented by `r`, `s` and `v`) must be provided as
were used to calculate the sighash during input signing.
The fraud challenge defeat attempt will only succeed if the
inputs in the preimage are considered honestly spent by the
wallet. Therefore the transaction spending the UTXO must be
proven in the Bridge before a challenge defeat is called.
If successfully defeated, the fraud challenge is marked as
resolved and the amount of ether deposited by the challenger is
sent to the treasury.

Requirements:
- `walletPublicKey` and `sighash` calculated as `hash256(preimage)`
must identify an open fraud challenge,
- the preimage must be a valid preimage of a transaction generated
according to the protocol rules and already proved in the Bridge,
- before a defeat attempt is made the transaction that spends the
given UTXO must be proven in the Bridge.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPublicKey | bytes | The public key of the wallet in the uncompressed and unprefixed format (64 bytes). |
| preimage | bytes | The preimage which produces sighash used to generate the ECDSA signature that is the subject of the fraud claim. It is a serialized subset of the transaction. The exact subset used as the preimage depends on the transaction input the signature is produced for. See BIP-143 for reference. |
| witness | bool | Flag indicating whether the preimage was produced for a witness input. True for witness, false for non-witness input. |

### defeatFraudChallengeWithHeartbeat

```solidity
function defeatFraudChallengeWithHeartbeat(struct BridgeState.Storage self, bytes walletPublicKey, bytes heartbeatMessage) external
```

Allows to defeat a pending fraud challenge against a wallet by
proving the sighash and signature were produced for an off-chain
wallet heartbeat message following a strict format.
In order to defeat the challenge the same `walletPublicKey` and
signature (represented by `r`, `s` and `v`) must be provided as
were used to calculate the sighash during heartbeat message
signing. The fraud challenge defeat attempt will only succeed if
the signed message follows a strict format required for
heartbeat messages. If successfully defeated, the fraud
challenge is marked as resolved and the amount of ether
deposited by the challenger is sent to the treasury.

Requirements:
- `walletPublicKey` and `sighash` calculated as
`hash256(heartbeatMessage)` must identify an open fraud challenge,
- `heartbeatMessage` must follow a strict format of heartbeat
messages.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPublicKey | bytes | The public key of the wallet in the uncompressed and unprefixed format (64 bytes), |
| heartbeatMessage | bytes | Off-chain heartbeat message meeting the heartbeat message format requirements which produces sighash used to generate the ECDSA signature that is the subject of the fraud claim. |

### resolveFraudChallenge

```solidity
function resolveFraudChallenge(struct BridgeState.Storage self, bytes walletPublicKey, struct Fraud.FraudChallenge challenge, bytes32 sighash) internal
```

Called only for successfully defeated fraud challenges.
The fraud challenge is marked as resolved and the amount of
ether deposited by the challenger is sent to the treasury.

Requirements:
- Must be called only for successfully defeated fraud challenges.

### notifyFraudChallengeDefeatTimeout

```solidity
function notifyFraudChallengeDefeatTimeout(struct BridgeState.Storage self, bytes walletPublicKey, uint32[] walletMembersIDs, bytes preimageSha256) external
```

Notifies about defeat timeout for the given fraud challenge.
Can be called only if there was a fraud challenge identified by
the provided `walletPublicKey` and `sighash` and it was not
defeated on time. The amount of time that needs to pass after
a fraud challenge is reported is indicated by the
`challengeDefeatTimeout`. After a successful fraud challenge
defeat timeout notification the fraud challenge is marked as
resolved, the stake of each operator is slashed, the ether
deposited is returned to the challenger and the challenger is
rewarded.

Requirements:
- The wallet must be in the Live or MovingFunds or Closing or
Terminated state,
- The `walletPublicKey` and `sighash` calculated from
`preimageSha256` must identify an open fraud challenge,
- The expression `keccak256(abi.encode(walletMembersIDs))` must
be exactly the same as the hash stored under `membersIdsHash`
for the given `walletID`. Those IDs are not directly stored
in the contract for gas efficiency purposes but they can be
read from appropriate `DkgResultSubmitted` and `DkgResultApproved`
events of the `WalletRegistry` contract,
- The amount of time indicated by `challengeDefeatTimeout` must pass
after the challenge was reported.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| walletPublicKey | bytes | The public key of the wallet in the uncompressed and unprefixed format (64 bytes). |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members. |
| preimageSha256 | bytes | The hash that was generated by applying SHA-256 one time over the preimage used during input signing. The preimage is a serialized subset of the transaction and its structure depends on the transaction input (see BIP-143 for reference). Notice that applying SHA-256 over the `preimageSha256` results in `sighash`.  The path from `preimage` to `sighash` looks like this: preimage -> (SHA-256) -> preimageSha256 -> (SHA-256) -> sighash. |

### extractUtxoKeyFromWitnessPreimage

```solidity
function extractUtxoKeyFromWitnessPreimage(bytes preimage) internal pure returns (uint256 utxoKey)
```

Extracts the UTXO keys from the given preimage used during
signing of a witness input.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| preimage | bytes | The preimage which produces sighash used to generate the ECDSA signature that is the subject of the fraud claim. It is a serialized subset of the transaction. The exact subset used as the preimage depends on the transaction input the signature is produced for. See BIP-143 for reference |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| utxoKey | uint256 | UTXO key that identifies spent input. |

### extractUtxoKeyFromNonWitnessPreimage

```solidity
function extractUtxoKeyFromNonWitnessPreimage(bytes preimage) internal pure returns (uint256 utxoKey)
```

Extracts the UTXO key from the given preimage used during
signing of a non-witness input.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| preimage | bytes | The preimage which produces sighash used to generate the ECDSA signature that is the subject of the fraud claim. It is a serialized subset of the transaction. The exact subset used as the preimage depends on the transaction input the signature is produced for. See BIP-143 for reference. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| utxoKey | uint256 | UTXO key that identifies spent input. |

### extractSighashType

```solidity
function extractSighashType(bytes preimage) internal pure returns (uint32 sighashType)
```

Extracts the sighash type from the given preimage.

Sighash type is stored as the last 4 bytes in the preimage (little
endian).

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| preimage | bytes | Serialized subset of the transaction. See BIP-143 for reference. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| sighashType | uint32 | Sighash type as a 32-bit integer. |

## Heartbeat

The library establishes expected format for heartbeat messages
signed by wallet ECDSA signing group. Heartbeat messages are
constructed in such a way that they can not be used as a Bitcoin
transaction preimages.

The smallest Bitcoin non-coinbase transaction is a one spending an
OP_TRUE anyonecanspend output and creating 1 OP_TRUE anyonecanspend
output. Such a transaction has 61 bytes (see `BitcoinTx` documentation):
4  bytes  for version
1  byte   for tx_in_count
36 bytes  for tx_in.previous_output
1  byte   for tx_in.script_bytes (value: 0)
0  bytes  for tx_in.signature_script
4  bytes  for tx_in.sequence
1  byte   for tx_out_count
8  bytes  for tx_out.value
1  byte   for tx_out.pk_script_bytes
1  byte   for tx_out.pk_script
4  bytes  for lock_time

The smallest Bitcoin coinbase transaction is a one creating
1 OP_TRUE anyonecanspend output and having an empty coinbase script.
Such a transaction has 65 bytes:
4  bytes  for version
1  byte   for tx_in_count
32 bytes  for tx_in.hash  (all 0x00)
4  bytes  for tx_in.index (all 0xff)
1  byte   for tx_in.script_bytes (value: 0)
4  bytes  for tx_in.height
0  byte   for tx_in.coinbase_script
4  bytes  for tx_in.sequence
1  byte   for tx_out_count
8  bytes  for tx_out.value
1  byte   for tx_out.pk_script_bytes
1  byte   for tx_out.pk_script
4  bytes  for lock_time

A SIGHASH flag is used to indicate which part of the transaction is
signed by the ECDSA signature. There are currently 3 flags:
SIGHASH_ALL, SIGHASH_NONE, SIGHASH_SINGLE, and different combinations
of these flags.

No matter the SIGHASH flag and no matter the combination, the following
fields from the transaction are always included in the constructed
preimage:
4  bytes  for version
36 bytes  for tx_in.previous_output (or tx_in.hash + tx_in.index for coinbase)
4  bytes  for lock_time

Additionally, the last 4 bytes of the preimage determines the SIGHASH
flag.

This is enough to say there is no way the preimage could be shorter
than 4 + 36 + 4 + 4 = 48 bytes.

For this reason, we construct the heartbeat message, as a 16-byte
message. The first 8 bytes are 0xffffffffffffffff. The last 8 bytes
are for an arbitrary uint64, being a signed heartbeat nonce (for
example, the last Ethereum block hash).

The message being signed by the wallet when executing the heartbeat
protocol should be Bitcoin's hash256 (double SHA-256) of the heartbeat
message:
heartbeat_sighash = hash256(heartbeat_message)

### isValidHeartbeatMessage

```solidity
function isValidHeartbeatMessage(bytes message) internal pure returns (bool)
```

Determines if the signed byte array is a valid, non-fraudulent
heartbeat message.

Wallet heartbeat message must be exactly 16 bytes long with the first
8 bytes set to 0xffffffffffffffff.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| message | bytes | Message signed by the wallet. It is a potential heartbeat message, Bitcoin transaction preimage, or an arbitrary signed bytes. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if valid heartbeat message, false otherwise. |

## IRelay

Contains only the methods needed by tBTC v2. The Bitcoin relay
provides the difficulty of the previous and current epoch. One
difficulty epoch spans 2016 blocks.

### getCurrentEpochDifficulty

```solidity
function getCurrentEpochDifficulty() external view returns (uint256)
```

Returns the difficulty of the current epoch.

### getPrevEpochDifficulty

```solidity
function getPrevEpochDifficulty() external view returns (uint256)
```

Returns the difficulty of the previous epoch.

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

## IVault

`IVault` is an interface for a smart contract consuming Bank
balances of other contracts or externally owned accounts (EOA).

### receiveBalanceIncrease

```solidity
function receiveBalanceIncrease(address[] depositors, uint256[] depositedAmounts) external
```

Called by the Bank in `increaseBalanceAndCall` function after
increasing the balance in the Bank for the vault. It happens in
the same transaction in which deposits were swept by the Bridge.
This allows the depositor to route their deposit revealed to the
Bridge to the particular smart contract (vault) in the same
transaction in which the deposit is revealed. This way, the
depositor does not have to execute additional transaction after
the deposit gets swept by the Bridge to approve and transfer
their balance to the vault.

The implementation must ensure this function can only be called
by the Bank. The Bank guarantees that the vault's balance was
increased by the sum of all deposited amounts before this function
is called, in the same transaction.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| depositors | address[] | Addresses of depositors whose deposits have been swept. |
| depositedAmounts | uint256[] | Amounts deposited by individual depositors and swept. |

## GovernanceUtils

### onlyAfterGovernanceDelay

```solidity
function onlyAfterGovernanceDelay(uint256 changeInitiatedTimestamp, uint256 delay) internal view
```

Reverts if the governance delay has not passed since
the change initiated time or if the change has not been
initiated.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| changeInitiatedTimestamp | uint256 | The timestamp at which the change has been initiated. |
| delay | uint256 | Governance delay. |

### getRemainingGovernanceDelay

```solidity
function getRemainingGovernanceDelay(uint256 changeInitiatedTimestamp, uint256 delay) internal view returns (uint256)
```

Gets the time remaining until the governable parameter update
can be committed.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| changeInitiatedTimestamp | uint256 | Timestamp indicating the beginning of the change. |
| delay | uint256 | Governance delay. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Remaining time in seconds. |

## VendingMachine

The Vending Machine is the owner of TBTC v2 token and can mint
TBTC v2 tokens in 1:1 ratio from TBTC v1 tokens with TBTC v1
deposited in the contract as collateral. TBTC v2 can be
unminted back to TBTC v1 with or without a fee - fee parameter is
controlled by the Governance. This implementation acts as a bridge
between TBTC v1 and TBTC v2 token, allowing to mint TBTC v2 before
the system is ready and fully operational without sacrificing any
security guarantees and decentralization of the project.
Vending Machine can be upgraded in a two-step, governance-controlled
process. The new version of the Vending Machine will receive the
ownership of TBTC v2 token and entire TBTC v1 balance stored as
collateral. It is expected that this process will be executed before
the v2 system launch. There is an optional unmint fee with a value
that can be updated in a two-step, governance-controlled process.
All governable parameters are controlled by two roles: update
initiator and finalizer. There is a separate initiator role for
unmint fee update and vending machine upgrade. The initiator
proposes the change by initiating the update and the finalizer
(contract owner) may approve it by finalizing the change after the
governance delay passes.

### GOVERNANCE_DELAY

```solidity
uint256 GOVERNANCE_DELAY
```

The time delay that needs to pass between initializing and
finalizing update of any governable parameter in this contract.

### FLOATING_POINT_DIVISOR

```solidity
uint256 FLOATING_POINT_DIVISOR
```

Divisor for precision purposes. Used to represent fractions
in parameter values.

### tbtcV1

```solidity
contract IERC20 tbtcV1
```

### tbtcV2

```solidity
contract TBTC tbtcV2
```

### unmintFee

```solidity
uint256 unmintFee
```

The fee for unminting TBTC v2 back into TBTC v1 represented as
1e18 precision fraction. The fee is proportional to the amount
being unminted and added on the top of the amount being unminted.
To calculate the fee value, the amount being unminted needs
to be multiplied by `unmintFee` and divided by 1e18.
For example, `unmintFee` set to 1000000000000000
means that 0.001 of the amount being unminted needs to be paid
to the `VendingMachine` as an unminting fee on the top of the
amount being unminted.

### newUnmintFee

```solidity
uint256 newUnmintFee
```

### unmintFeeUpdateInitiatedTimestamp

```solidity
uint256 unmintFeeUpdateInitiatedTimestamp
```

### unmintFeeUpdateInitiator

```solidity
address unmintFeeUpdateInitiator
```

### newVendingMachine

```solidity
address newVendingMachine
```

The address of a new vending machine. Set only when the upgrade
process is pending. Once the upgrade gets finalized, the new
vending machine will become an owner of TBTC v2 token.

### vendingMachineUpgradeInitiatedTimestamp

```solidity
uint256 vendingMachineUpgradeInitiatedTimestamp
```

### vendingMachineUpgradeInitiator

```solidity
address vendingMachineUpgradeInitiator
```

### UnmintFeeUpdateInitiated

```solidity
event UnmintFeeUpdateInitiated(uint256 newUnmintFee, uint256 timestamp)
```

### UnmintFeeUpdated

```solidity
event UnmintFeeUpdated(uint256 newUnmintFee)
```

### VendingMachineUpgradeInitiated

```solidity
event VendingMachineUpgradeInitiated(address newVendingMachine, uint256 timestamp)
```

### VendingMachineUpgraded

```solidity
event VendingMachineUpgraded(address newVendingMachine)
```

### Minted

```solidity
event Minted(address recipient, uint256 amount)
```

### Unminted

```solidity
event Unminted(address recipient, uint256 amount, uint256 fee)
```

### only

```solidity
modifier only(address authorizedCaller)
```

### onlyAfterGovernanceDelay

```solidity
modifier onlyAfterGovernanceDelay(uint256 changeInitiatedTimestamp)
```

### constructor

```solidity
constructor(contract IERC20 _tbtcV1, contract TBTC _tbtcV2, uint256 _unmintFee) public
```

### mint

```solidity
function mint(uint256 amount) external
```

Mints TBTC v2 to the caller from TBTC v1 with 1:1 ratio.
The caller needs to have at least `amount` of TBTC v1 balance
approved for transfer to the `VendingMachine` before calling
this function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | The amount of TBTC v2 to mint from TBTC v1 |

### receiveApproval

```solidity
function receiveApproval(address from, uint256 amount, address token, bytes) external
```

Mints TBTC v2 to `from` address from TBTC v1 with 1:1 ratio.
`from` address needs to have at least `amount` of TBTC v1
balance approved for transfer to the `VendingMachine` before
calling this function.

This function is a shortcut for approve + mint. Only TBTC v1
caller is allowed and only TBTC v1 is allowed as a token to
transfer.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| from | address | TBTC v1 token holder minting TBTC v2 tokens |
| amount | uint256 | The amount of TBTC v2 to mint from TBTC v1 |
| token | address | TBTC v1 token address |
|  | bytes |  |

### unmint

```solidity
function unmint(uint256 amount) external
```

Unmints TBTC v2 from the caller into TBTC v1. Depending on
`unmintFee` value, may require paying an additional unmint fee
in TBTC v2 in addition to the amount being unminted. To see
what is the value of the fee, please call `unmintFeeFor(amount)`
function. The caller needs to have at least
`amount + unmintFeeFor(amount)` of TBTC v2 balance approved for
transfer to the `VendingMachine` before calling this function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | The amount of TBTC v2 to unmint to TBTC v1 |

### withdrawFees

```solidity
function withdrawFees(address recipient, uint256 amount) external
```

Allows the Governance to withdraw unmint fees accumulated by
`VendingMachine`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| recipient | address | The address receiving the fees |
| amount | uint256 | The amount of fees in TBTC v2 to withdraw |

### initiateUnmintFeeUpdate

```solidity
function initiateUnmintFeeUpdate(uint256 _newUnmintFee) external
```

Initiates unmint fee update process. The update process needs to
be finalized with a call to `finalizeUnmintFeeUpdate` function
after the `GOVERNANCE_DELAY` passes. Only unmint fee update
initiator role can initiate the update.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newUnmintFee | uint256 | The new unmint fee |

### finalizeUnmintFeeUpdate

```solidity
function finalizeUnmintFeeUpdate() external
```

Allows the contract owner to finalize unmint fee update process.
The update process needs to be first initiated with a call to
`initiateUnmintFeeUpdate` and the `GOVERNANCE_DELAY` needs to
pass.

### initiateVendingMachineUpgrade

```solidity
function initiateVendingMachineUpgrade(address _newVendingMachine) external
```

Initiates vending machine upgrade process. The upgrade process
needs to be finalized with a call to
`finalizeVendingMachineUpgrade` function after the
`GOVERNANCE_DELAY` passes. Only vending machine upgrade
initiator role can initiate the upgrade.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newVendingMachine | address | The new vending machine address |

### finalizeVendingMachineUpgrade

```solidity
function finalizeVendingMachineUpgrade() external
```

Allows the contract owner to finalize vending machine upgrade
process. The upgrade process needs to be first initiated with a
call to `initiateVendingMachineUpgrade` and the `GOVERNANCE_DELAY`
needs to pass. Once the upgrade is finalized, the new vending
machine will become an owner of TBTC v2 token and all TBTC v1
held by this contract will be transferred to the new vending
machine.

### transferUnmintFeeUpdateInitiatorRole

```solidity
function transferUnmintFeeUpdateInitiatorRole(address newInitiator) external
```

Transfers unmint fee update initiator role to another address.
Can be called only by the current unmint fee update initiator.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| newInitiator | address | The new unmint fee update initiator |

### transferVendingMachineUpgradeInitiatorRole

```solidity
function transferVendingMachineUpgradeInitiatorRole(address newInitiator) external
```

Transfers vending machine upgrade initiator role to another
address. Can be called only by the current vending machine
upgrade initiator.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| newInitiator | address | The new vending machine upgrade initiator |

### getRemainingUnmintFeeUpdateTime

```solidity
function getRemainingUnmintFeeUpdateTime() external view returns (uint256)
```

Get the remaining time that needs to pass until unmint fee
update can be finalized by the Governance. If the update has
not been initiated, the function reverts.

### getRemainingVendingMachineUpgradeTime

```solidity
function getRemainingVendingMachineUpgradeTime() external view returns (uint256)
```

Get the remaining time that needs to pass until vending machine
upgrade can be finalized by the Governance. If the upgrade has
not been initiated, the function reverts.

### unmintFeeFor

```solidity
function unmintFeeFor(uint256 amount) public view returns (uint256)
```

Calculates the fee that needs to be paid to the `VendingMachine`
to unmint the given amount of TBTC v2 back into TBTC v1.

### _mint

```solidity
function _mint(address tokenOwner, uint256 amount) internal
```

## VendingMachineV2

VendingMachineV2 is used to exchange tBTC v1 to tBTC v2 in a 1:1
ratio during the process of tBTC v1 bridge sunsetting. The redeemer
selected by the DAO based on the "TIP-027b tBTC v1: The Sunsetting"
proposal will deposit tBTC v2 tokens into VendingMachineV2 so that
outstanding tBTC v1 token owners can  upgrade to tBTC v2 tokens.
The redeemer will withdraw the tBTC v1 tokens deposited into the
contract to perform tBTC v1 redemptions.
The redeemer may decide to withdraw their deposited tBTC v2 at any
moment in time. The amount withdrawable is lower than the amount
deposited in case tBTC v1 was exchanged for tBTC v2.
This contract is owned by the redeemer.

### tbtcV1

```solidity
contract IERC20 tbtcV1
```

### tbtcV2

```solidity
contract TBTC tbtcV2
```

### Exchanged

```solidity
event Exchanged(address to, uint256 amount)
```

### Deposited

```solidity
event Deposited(address from, uint256 amount)
```

### Withdrawn

```solidity
event Withdrawn(address token, address to, uint256 amount)
```

### constructor

```solidity
constructor(contract IERC20 _tbtcV1, contract TBTC _tbtcV2) public
```

### exchange

```solidity
function exchange(uint256 amount) external
```

Exchange tBTC v1 for tBTC v2 in a 1:1 ratio.
The caller needs to have at least `amount` of tBTC v1 balance
approved for transfer to the `VendingMachineV2` before calling
this function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | The amount of tBTC v1 to exchange for tBTC v2. |

### receiveApproval

```solidity
function receiveApproval(address from, uint256 amount, address token, bytes) external
```

Exchange tBTC v1 for tBTC v2 in a 1:1 ratio.
The caller needs to have at least `amount` of tBTC v1 balance
approved for transfer to the `VendingMachineV2` before calling
this function.

This function is a shortcut for `approve` + `exchange`. Only tBTC
v1 token caller is allowed and only tBTC v1 is allowed as a token
to transfer.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| from | address | tBTC v1 token holder exchanging tBTC v1 to tBTC v2. |
| amount | uint256 | The amount of tBTC v1 to exchange for tBTC v2. |
| token | address | tBTC v1 token address. |
|  | bytes |  |

### depositTbtcV2

```solidity
function depositTbtcV2(uint256 amount) external
```

Allows to deposit tBTC v2 tokens to the contract.
VendingMachineV2 can not mint tBTC v2 tokens so tBTC v2 needs
to be deposited into the contract so that tBTC v1 to tBTC v2
exchange can happen.
The caller needs to have at least `amount` of tBTC v2 balance
approved for transfer to the `VendingMachineV2` before calling
this function.

This function is for the redeemer and tBTC v1 operators. This is
NOT a function for tBTC v1 token holders.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | The amount of tBTC v2 to deposit into the contract. |

### withdrawFunds

```solidity
function withdrawFunds(contract IERC20 token, address recipient, uint256 amount) external
```

Allows the contract owner to withdraw tokens. This function is
used in two cases: 1) when the redeemer wants to redeem tBTC v1
tokens to perform tBTC v2 redemptions; 2) when the deadline for
tBTC v1 -> tBTC v2 exchange passed and the redeemer wants their
tBTC v2 back.

This function is for the redeemer. This is NOT a function for
tBTC v1 token holders.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| token | contract IERC20 | The address of a token to withdraw. |
| recipient | address | The address which should receive withdrawn tokens. |
| amount | uint256 | The amount to withdraw. |

### _exchange

```solidity
function _exchange(address tokenOwner, uint256 amount) internal
```

## VendingMachineV3

VendingMachineV3 is used to exchange tBTC v1 to tBTC v2 in a 1:1
ratio after the tBTC v1 bridge sunsetting is completed. Since
tBTC v1 bridge is no longer working, tBTC v1 tokens can not be used
to perform BTC redemptions. This contract allows tBTC v1 owners to
upgrade to tBTC v2 without any deadline. This way, tBTC v1 tokens
left on the market are always backed by Bitcoin. The governance will
deposit tBTC v2 into the contract in the amount equal to tBTC v1
supply. The governance is allowed to withdraw tBTC v2 only if tBTC
v2 left in this contract is enough to cover the upgrade of all tBTC
v1 left on the market. This contract is owned by the governance.

### tbtcV1

```solidity
contract IERC20 tbtcV1
```

### tbtcV2

```solidity
contract TBTC tbtcV2
```

### Exchanged

```solidity
event Exchanged(address to, uint256 amount)
```

### Deposited

```solidity
event Deposited(address from, uint256 amount)
```

### TbtcV2Withdrawn

```solidity
event TbtcV2Withdrawn(address to, uint256 amount)
```

### FundsRecovered

```solidity
event FundsRecovered(address token, address to, uint256 amount)
```

### constructor

```solidity
constructor(contract IERC20 _tbtcV1, contract TBTC _tbtcV2) public
```

### exchange

```solidity
function exchange(uint256 amount) external
```

Exchange tBTC v1 for tBTC v2 in a 1:1 ratio.
The caller needs to have at least `amount` of tBTC v1 balance
approved for transfer to the `VendingMachineV3` before calling
this function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | The amount of tBTC v1 to exchange for tBTC v2. |

### receiveApproval

```solidity
function receiveApproval(address from, uint256 amount, address token, bytes) external
```

Exchange tBTC v1 for tBTC v2 in a 1:1 ratio.
The caller needs to have at least `amount` of tBTC v1 balance
approved for transfer to the `VendingMachineV3` before calling
this function.

This function is a shortcut for `approve` + `exchange`. Only tBTC
v1 caller is allowed and only tBTC v1 is allowed as a token to
transfer.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| from | address | tBTC v1 token holder exchanging tBTC v1 to tBTC v2. |
| amount | uint256 | The amount of tBTC v1 to exchange for tBTC v2. |
| token | address | tBTC v1 token address. |
|  | bytes |  |

### depositTbtcV2

```solidity
function depositTbtcV2(uint256 amount) external
```

Allows to deposit tBTC v2 tokens to the contract.
`VendingMachineV3` can not mint tBTC v2 tokens so tBTC v2 needs
to be deposited into the contract so that tBTC v1 to tBTC v2
exchange can happen.
The caller needs to have at least `amount` of tBTC v2 balance
approved for transfer to the `VendingMachineV3` before calling
this function.

This function is for the redeemer and tBTC v1 operators. This is
NOT a function for tBTC v1 token holders.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | The amount of tBTC v2 to deposit into the contract. |

### withdrawTbtcV2

```solidity
function withdrawTbtcV2(address recipient, uint256 amount) external
```

Allows the governance to withdraw tBTC v2 deposited into this
contract. The governance is allowed to withdraw tBTC v2
only if tBTC v2 left in this contract is enough to cover the
upgrade of all tBTC v1 left on the market.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| recipient | address | The address which should receive withdrawn tokens. |
| amount | uint256 | The amount to withdraw. |

### recoverFunds

```solidity
function recoverFunds(contract IERC20 token, address recipient, uint256 amount) external
```

Allows the governance to recover ERC20 sent to this contract
by mistake or tBTC v1 locked in the contract to exchange to
tBTC v2. No tBTC v2 can be withdrawn using this function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| token | contract IERC20 | The address of a token to recover. |
| recipient | address | The address which should receive recovered tokens. |
| amount | uint256 | The amount to recover. |

### _exchange

```solidity
function _exchange(address tokenOwner, uint256 amount) internal
```

## WalletCoordinator

The wallet coordinator contract aims to facilitate the coordination
of the off-chain wallet members during complex multi-chain wallet
operations like deposit sweeping, redemptions, or moving funds.
Such processes involve various moving parts and many steps that each
individual wallet member must do. Given the distributed nature of
the off-chain wallet software, full off-chain implementation is
challenging and prone to errors, especially byzantine faults.
This contract provides a single and trusted on-chain coordination
point thus taking the riskiest part out of the off-chain software.
The off-chain wallet members can focus on the core tasks and do not
bother about electing a trusted coordinator or aligning internal
states using complex consensus algorithms.

### WalletAction

```solidity
enum WalletAction {
  Idle,
  Heartbeat,
  DepositSweep,
  Redemption,
  MovingFunds,
  MovedFundsSweep
}
```

### WalletLock

```solidity
struct WalletLock {
  uint32 expiresAt;
  enum WalletCoordinator.WalletAction cause;
}
```

### DepositSweepProposal

```solidity
struct DepositSweepProposal {
  bytes20 walletPubKeyHash;
  struct WalletCoordinator.DepositKey[] depositsKeys;
  uint256 sweepTxFee;
  uint256[] depositsRevealBlocks;
}
```

### DepositKey

```solidity
struct DepositKey {
  bytes32 fundingTxHash;
  uint32 fundingOutputIndex;
}
```

### DepositExtraInfo

```solidity
struct DepositExtraInfo {
  struct BitcoinTx.Info fundingTx;
  bytes8 blindingFactor;
  bytes20 walletPubKeyHash;
  bytes20 refundPubKeyHash;
  bytes4 refundLocktime;
}
```

### RedemptionProposal

```solidity
struct RedemptionProposal {
  bytes20 walletPubKeyHash;
  bytes[] redeemersOutputScripts;
  uint256 redemptionTxFee;
}
```

### isCoordinator

```solidity
mapping(address => bool) isCoordinator
```

Mapping that holds addresses allowed to submit proposals and
request heartbeats.

### walletLock

```solidity
mapping(bytes20 => struct WalletCoordinator.WalletLock) walletLock
```

Mapping that holds wallet time locks. The key is a 20-byte
wallet public key hash.

### bridge

```solidity
contract Bridge bridge
```

Handle to the Bridge contract.

### heartbeatRequestValidity

```solidity
uint32 heartbeatRequestValidity
```

Determines the wallet heartbeat request validity time. In other
words, this is  the worst-case time for a wallet heartbeat
during which the wallet is busy and canot take other actions.
This is also the duration of the time lock applied to the wallet
once a new heartbeat request is submitted.

For example, if a deposit sweep proposal was submitted at
2 pm and heartbeatRequestValidity is 1 hour, the next request or
proposal (of any type) can be submitted after 3 pm.

### heartbeatRequestGasOffset

```solidity
uint32 heartbeatRequestGasOffset
```

Gas that is meant to balance the heartbeat request overall cost.
Can be updated by the owner based on the current conditions.

### depositSweepProposalValidity

```solidity
uint32 depositSweepProposalValidity
```

Determines the deposit sweep proposal validity time. In other
words, this is the worst-case time for a deposit sweep during
which the wallet is busy and cannot take another actions. This
is also the duration of the time lock applied to the wallet
once a new deposit sweep proposal is submitted.

For example, if a deposit sweep proposal was submitted at
2 pm and depositSweepProposalValidity is 4 hours, the next
proposal (of any type) can be submitted after 6 pm.

### depositMinAge

```solidity
uint32 depositMinAge
```

The minimum time that must elapse since the deposit reveal
before a deposit becomes eligible for a deposit sweep.

For example, if a deposit was revealed at 9 am and depositMinAge
is 2 hours, the deposit is eligible for sweep after 11 am.

Forcing deposit minimum age ensures block finality for Ethereum.
In the happy path case, i.e. where the deposit is revealed immediately
after being broadcast on the Bitcoin network, the minimum age
check also ensures block finality for Bitcoin.

### depositRefundSafetyMargin

```solidity
uint32 depositRefundSafetyMargin
```

Each deposit can be technically swept until it reaches its
refund timestamp after which it can be taken back by the depositor.
However, allowing the wallet to sweep deposits that are close
to their refund timestamp may cause a race between the wallet
and the depositor. In result, the wallet may sign an invalid
sweep transaction that aims to sweep an already refunded deposit.
Such tx signature may be used to create an undefeatable fraud
challenge against the wallet. In order to mitigate that problem,
this parameter determines a safety margin that puts the latest
moment a deposit can be swept far before the point after which
the deposit becomes refundable.

For example, if a deposit becomes refundable after 8 pm and
depositRefundSafetyMargin is 6 hours, the deposit is valid for
for a sweep only before 2 pm.

### depositSweepMaxSize

```solidity
uint16 depositSweepMaxSize
```

The maximum count of deposits that can be swept within a
single sweep.

### depositSweepProposalSubmissionGasOffset

```solidity
uint32 depositSweepProposalSubmissionGasOffset
```

Gas that is meant to balance the deposit sweep proposal
submission overall cost. Can be updated by the owner based on
the current conditions.

### redemptionProposalValidity

```solidity
uint32 redemptionProposalValidity
```

Determines the redemption proposal validity time. In other
words, this is the worst-case time for a redemption during
which the wallet is busy and cannot take another actions. This
is also the duration of the time lock applied to the wallet
once a new redemption proposal is submitted.

For example, if a redemption proposal was submitted at
2 pm and redemptionProposalValidity is 2 hours, the next
proposal (of any type) can be submitted after 4 pm.

### redemptionRequestMinAge

```solidity
uint32 redemptionRequestMinAge
```

The minimum time that must elapse since the redemption request
creation before a request becomes eligible for a processing.

For example, if a request was created at 9 am and
redemptionRequestMinAge is 2 hours, the request is eligible for
processing after 11 am.

Forcing request minimum age ensures block finality for Ethereum.

### redemptionRequestTimeoutSafetyMargin

```solidity
uint32 redemptionRequestTimeoutSafetyMargin
```

Each redemption request can be technically handled until it
reaches its timeout timestamp after which it can be reported
as timed out. However, allowing the wallet to handle requests
that are close to their timeout timestamp may cause a race
between the wallet and the redeemer. In result, the wallet may
redeem the requested funds even though the redeemer already
received back their tBTC (locked during redemption request) upon
reporting the request timeout. In effect, the redeemer may end
out with both tBTC and redeemed BTC in their hands which has
a negative impact on the tBTC <-> BTC peg. In order to mitigate
that problem, this parameter determines a safety margin that
puts the latest moment a request can be handled far before the
point after which the request can be reported as timed out.

For example, if a request times out after 8 pm and
redemptionRequestTimeoutSafetyMargin is 2 hours, the request is
valid for processing only before 6 pm.

### redemptionMaxSize

```solidity
uint16 redemptionMaxSize
```

The maximum count of redemption requests that can be processed
within a single redemption.

### redemptionProposalSubmissionGasOffset

```solidity
uint32 redemptionProposalSubmissionGasOffset
```

Gas that is meant to balance the redemption proposal
submission overall cost. Can be updated by the owner based on
the current conditions.

### CoordinatorAdded

```solidity
event CoordinatorAdded(address coordinator)
```

### CoordinatorRemoved

```solidity
event CoordinatorRemoved(address coordinator)
```

### WalletManuallyUnlocked

```solidity
event WalletManuallyUnlocked(bytes20 walletPubKeyHash)
```

### HeartbeatRequestParametersUpdated

```solidity
event HeartbeatRequestParametersUpdated(uint32 heartbeatRequestValidity, uint32 heartbeatRequestGasOffset)
```

### HeartbeatRequestSubmitted

```solidity
event HeartbeatRequestSubmitted(bytes20 walletPubKeyHash, bytes message, address coordinator)
```

### DepositSweepProposalParametersUpdated

```solidity
event DepositSweepProposalParametersUpdated(uint32 depositSweepProposalValidity, uint32 depositMinAge, uint32 depositRefundSafetyMargin, uint16 depositSweepMaxSize, uint32 depositSweepProposalSubmissionGasOffset)
```

### DepositSweepProposalSubmitted

```solidity
event DepositSweepProposalSubmitted(struct WalletCoordinator.DepositSweepProposal proposal, address coordinator)
```

### RedemptionProposalParametersUpdated

```solidity
event RedemptionProposalParametersUpdated(uint32 redemptionProposalValidity, uint32 redemptionRequestMinAge, uint32 redemptionRequestTimeoutSafetyMargin, uint16 redemptionMaxSize, uint32 redemptionProposalSubmissionGasOffset)
```

### RedemptionProposalSubmitted

```solidity
event RedemptionProposalSubmitted(struct WalletCoordinator.RedemptionProposal proposal, address coordinator)
```

### onlyCoordinator

```solidity
modifier onlyCoordinator()
```

### onlyAfterWalletLock

```solidity
modifier onlyAfterWalletLock(bytes20 walletPubKeyHash)
```

### onlyReimbursableAdmin

```solidity
modifier onlyReimbursableAdmin()
```

### initialize

```solidity
function initialize(contract Bridge _bridge) external
```

### addCoordinator

```solidity
function addCoordinator(address coordinator) external
```

Adds the given address to the set of coordinator addresses.

Requirements:
- The caller must be the owner,
- The `coordinator` must not be an existing coordinator.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| coordinator | address | Address of the new coordinator. |

### removeCoordinator

```solidity
function removeCoordinator(address coordinator) external
```

Removes the given address from the set of coordinator addresses.

Requirements:
- The caller must be the owner,
- The `coordinator` must be an existing coordinator.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| coordinator | address | Address of the existing coordinator. |

### unlockWallet

```solidity
function unlockWallet(bytes20 walletPubKeyHash) external
```

Allows to unlock the given wallet before their time lock expires.
This function should be used in exceptional cases where
something went wrong and there is a need to unlock the wallet
without waiting.

Requirements:
- The caller must be the owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet |

### updateHeartbeatRequestParameters

```solidity
function updateHeartbeatRequestParameters(uint32 _heartbeatRequestValidity, uint32 _heartbeatRequestGasOffset) external
```

Updates parameters related to heartbeat request.

Requirements:
- The caller must be the owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _heartbeatRequestValidity | uint32 | The new value of `heartbeatRequestValidity`. |
| _heartbeatRequestGasOffset | uint32 | The new value of `heartbeatRequestGasOffset`. |

### updateDepositSweepProposalParameters

```solidity
function updateDepositSweepProposalParameters(uint32 _depositSweepProposalValidity, uint32 _depositMinAge, uint32 _depositRefundSafetyMargin, uint16 _depositSweepMaxSize, uint32 _depositSweepProposalSubmissionGasOffset) external
```

Updates parameters related to deposit sweep proposal.

Requirements:
- The caller must be the owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _depositSweepProposalValidity | uint32 | The new value of `depositSweepProposalValidity`. |
| _depositMinAge | uint32 | The new value of `depositMinAge`. |
| _depositRefundSafetyMargin | uint32 | The new value of `depositRefundSafetyMargin`. |
| _depositSweepMaxSize | uint16 | The new value of `depositSweepMaxSize`. |
| _depositSweepProposalSubmissionGasOffset | uint32 |  |

### requestHeartbeat

```solidity
function requestHeartbeat(bytes20 walletPubKeyHash, bytes message) public
```

Submits a heartbeat request from the wallet. Locks the wallet
for a specific time, equal to the request validity period.
This function validates the proposed heartbeat messge to see
if it matches the heartbeat format expected by the Bridge.

Requirements:
- The caller is a coordinator,
- The wallet is not time-locked,
- The message to sign is a valid heartbeat message.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletPubKeyHash | bytes20 | 20-byte public key hash of the wallet that is supposed to execute the heartbeat. |
| message | bytes | The proposed heartbeat message for the wallet to sign. |

### requestHeartbeatWithReimbursement

```solidity
function requestHeartbeatWithReimbursement(bytes20 walletPubKeyHash, bytes message) external
```

Wraps `requestHeartbeat` call and reimburses the caller's
transaction cost.

See `requestHeartbeat` function documentation.

### submitDepositSweepProposal

```solidity
function submitDepositSweepProposal(struct WalletCoordinator.DepositSweepProposal proposal) public
```

Submits a deposit sweep proposal. Locks the target wallet
for a specific time, equal to the proposal validity period.
This function does not store the proposal in the state but
just emits an event that serves as a guiding light for wallet
off-chain members. Wallet members are supposed to validate
the proposal on their own, before taking any action.

Requirements:
- The caller is a coordinator,
- The wallet is not time-locked.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| proposal | struct WalletCoordinator.DepositSweepProposal | The deposit sweep proposal |

### submitDepositSweepProposalWithReimbursement

```solidity
function submitDepositSweepProposalWithReimbursement(struct WalletCoordinator.DepositSweepProposal proposal) external
```

Wraps `submitDepositSweepProposal` call and reimburses the
caller's transaction cost.

See `submitDepositSweepProposal` function documentation.

### validateDepositSweepProposal

```solidity
function validateDepositSweepProposal(struct WalletCoordinator.DepositSweepProposal proposal, struct WalletCoordinator.DepositExtraInfo[] depositsExtraInfo) external view returns (bool)
```

View function encapsulating the main rules of a valid deposit
sweep proposal. This function is meant to facilitate the off-chain
validation of the incoming proposals. Thanks to it, most
of the work can be done using a single readonly contract call.
Worth noting, the validation done here is not exhaustive as some
conditions may not be verifiable within the on-chain function or
checking them may be easier on the off-chain side. For example,
this function does not check the SPV proofs and confirmations of
the deposit funding transactions as this would require an
integration with the difficulty relay that greatly increases
complexity. Instead of that, each off-chain wallet member is
supposed to do that check on their own.

Requirements:
- The target wallet must be in the Live state,
- The number of deposits included in the sweep must be in
the range [1, `depositSweepMaxSize`],
- The length of `depositsExtraInfo` array must be equal to the
length of `proposal.depositsKeys`, i.e. each deposit must
have exactly one set of corresponding extra data,
- The proposed sweep tx fee must be grater than zero,
- The proposed maximum per-deposit sweep tx fee must be lesser than
or equal the maximum fee allowed by the Bridge (`Bridge.depositTxMaxFee`),
- Each deposit must be revealed to the Bridge,
- Each deposit must be old enough, i.e. at least `depositMinAge`
elapsed since their reveal time,
- Each deposit must not be swept yet,
- Each deposit must have valid extra data (see `validateDepositExtraInfo`),
- Each deposit must have the refund safety margin preserved,
- Each deposit must be controlled by the same wallet,
- Each deposit must target the same vault,
- Each deposit must be unique.

The following off-chain validation must be performed as a bare minimum:
- Inputs used for the sweep transaction have enough Bitcoin confirmations,
- Deposits revealed to the Bridge have enough Ethereum confirmations.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| proposal | struct WalletCoordinator.DepositSweepProposal | The sweeping proposal to validate. |
| depositsExtraInfo | struct WalletCoordinator.DepositExtraInfo[] | Deposits extra data required to perform the validation. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if the proposal is valid. Reverts otherwise. |

### validateSweepTxFee

```solidity
function validateSweepTxFee(uint256 sweepTxFee, uint256 depositsCount) internal view
```

Validates the sweep tx fee by checking if the part of the fee
incurred by each deposit does not exceed the maximum value
allowed by the Bridge. This function is heavily based on
`DepositSweep.depositSweepTxFeeDistribution` function.

Requirements:
- The sweep tx fee must be grater than zero,
- The maximum per-deposit sweep tx fee must be lesser than or equal
the maximum fee allowed by the Bridge (`Bridge.depositTxMaxFee`).

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| sweepTxFee | uint256 | The sweep transaction fee. |
| depositsCount | uint256 | Count of the deposits swept by the sweep transaction. |

### validateDepositExtraInfo

```solidity
function validateDepositExtraInfo(struct WalletCoordinator.DepositKey depositKey, address depositor, struct WalletCoordinator.DepositExtraInfo depositExtraInfo) internal view
```

Validates the extra data for the given deposit. This function
is heavily based on `Deposit.revealDeposit` function.

Requirements:
- The transaction hash computed using `depositExtraInfo.fundingTx`
must match the `depositKey.fundingTxHash`. This requirement
ensures the funding transaction data provided in the extra
data container actually represent the funding transaction of
the given deposit.
- The P2(W)SH script inferred from `depositExtraInfo` is actually
used to lock funds by the `depositKey.fundingOutputIndex` output
of the `depositExtraInfo.fundingTx` transaction. This requirement
ensures the reveal data provided in the extra data container
actually matches the given deposit.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| depositKey | struct WalletCoordinator.DepositKey | Key of the given deposit. |
| depositor | address | Depositor that revealed the deposit. |
| depositExtraInfo | struct WalletCoordinator.DepositExtraInfo | Extra data being subject of the validation. |

### updateRedemptionProposalParameters

```solidity
function updateRedemptionProposalParameters(uint32 _redemptionProposalValidity, uint32 _redemptionRequestMinAge, uint32 _redemptionRequestTimeoutSafetyMargin, uint16 _redemptionMaxSize, uint32 _redemptionProposalSubmissionGasOffset) external
```

Updates parameters related to redemption proposal.

Requirements:
- The caller must be the owner.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _redemptionProposalValidity | uint32 | The new value of `redemptionProposalValidity`. |
| _redemptionRequestMinAge | uint32 | The new value of `redemptionRequestMinAge`. |
| _redemptionRequestTimeoutSafetyMargin | uint32 | The new value of `redemptionRequestTimeoutSafetyMargin`. |
| _redemptionMaxSize | uint16 | The new value of `redemptionMaxSize`. |
| _redemptionProposalSubmissionGasOffset | uint32 | The new value of `redemptionProposalSubmissionGasOffset`. |

### submitRedemptionProposal

```solidity
function submitRedemptionProposal(struct WalletCoordinator.RedemptionProposal proposal) public
```

Submits a redemption proposal. Locks the target wallet
for a specific time, equal to the proposal validity period.
This function does not store the proposal in the state but
just emits an event that serves as a guiding light for wallet
off-chain members. Wallet members are supposed to validate
the proposal on their own, before taking any action.

Requirements:
- The caller is a coordinator,
- The wallet is not time-locked.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| proposal | struct WalletCoordinator.RedemptionProposal | The redemption proposal |

### submitRedemptionProposalWithReimbursement

```solidity
function submitRedemptionProposalWithReimbursement(struct WalletCoordinator.RedemptionProposal proposal) external
```

Wraps `submitRedemptionProposal` call and reimburses the
caller's transaction cost.

See `submitRedemptionProposal` function documentation.

### validateRedemptionProposal

```solidity
function validateRedemptionProposal(struct WalletCoordinator.RedemptionProposal proposal) external view returns (bool)
```

View function encapsulating the main rules of a valid redemption
proposal. This function is meant to facilitate the off-chain
validation of the incoming proposals. Thanks to it, most
of the work can be done using a single readonly contract call.

Requirements:
- The target wallet must be in the Live state,
- The number of redemption requests included in the redemption
proposal must be in the range [1, `redemptionMaxSize`],
- The proposed redemption tx fee must be grater than zero,
- The proposed redemption tx fee must be lesser than or equal to
the maximum total fee allowed by the Bridge
(`Bridge.redemptionTxMaxTotalFee`),
- The proposed maximum per-request redemption tx fee share must be
lesser than or equal to the maximum fee share allowed by the
given request (`RedemptionRequest.txMaxFee`),
- Each request must be a pending request registered in the Bridge,
- Each request must be old enough, i.e. at least `redemptionRequestMinAge`
elapsed since their creation time,
- Each request must have the timeout safety margin preserved,
- Each request must be unique.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| proposal | struct WalletCoordinator.RedemptionProposal | The redemption proposal to validate. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True if the proposal is valid. Reverts otherwise. |

## L2TBTC

Canonical L2/sidechain token implementation. tBTC token is minted on
L1 and locked there to be moved to L2/sidechain. By deploying
a canonical token on each L2/sidechain, we can ensure the supply of
tBTC remains sacrosanct, while enabling quick, interoperable
cross-chain bridges and localizing ecosystem risk.

This contract is flexible enough to:
- Delegate minting authority to a native bridge on the chain, if
present.
- Delegate minting authority to a short list of ecosystem bridges.
- Have mints and burns paused by any one of n guardians, allowing
avoidance of contagion in case of a chain- or bridge-specific
incident.
- Be governed and upgradeable.

The token is burnable by the token holder and supports EIP2612
permits. Token holder can authorize a transfer of their token with
a signature conforming EIP712 standard instead of an on-chain
transaction from their address. Anyone can submit this signature on
the user's behalf by calling the permit function, paying gas fees,
and possibly performing other actions in the same transaction.
The governance can recover ERC20 and ERC721 tokens sent mistakenly
to L2TBTC token contract.

### isMinter

```solidity
mapping(address => bool) isMinter
```

Indicates if the given address is a minter. Only minters can
mint the token.

### minters

```solidity
address[] minters
```

List of all minters.

### isGuardian

```solidity
mapping(address => bool) isGuardian
```

Indicates if the given address is a guardian. Only guardians can
pause token mints and burns.

### guardians

```solidity
address[] guardians
```

List of all guardians.

### MinterAdded

```solidity
event MinterAdded(address minter)
```

### MinterRemoved

```solidity
event MinterRemoved(address minter)
```

### GuardianAdded

```solidity
event GuardianAdded(address guardian)
```

### GuardianRemoved

```solidity
event GuardianRemoved(address guardian)
```

### onlyMinter

```solidity
modifier onlyMinter()
```

### onlyGuardian

```solidity
modifier onlyGuardian()
```

### initialize

```solidity
function initialize(string _name, string _symbol) external
```

Initializes the token contract.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _name | string | The name of the token. |
| _symbol | string | The symbol of the token, usually a shorter version of the name. |

### addMinter

```solidity
function addMinter(address minter) external
```

Adds the address to the minters list.

Requirements:
- The caller must be the contract owner.
- `minter` must not be a minter address already.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| minter | address | The address to be added as a minter. |

### removeMinter

```solidity
function removeMinter(address minter) external
```

Removes the address from the minters list.

Requirements:
- The caller must be the contract owner.
- `minter` must be a minter address.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| minter | address | The address to be removed from the minters list. |

### addGuardian

```solidity
function addGuardian(address guardian) external
```

Adds the address to the guardians list.

Requirements:
- The caller must be the contract owner.
- `guardian` must not be a guardian address already.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| guardian | address | The address to be added as a guardian. |

### removeGuardian

```solidity
function removeGuardian(address guardian) external
```

Removes the address from the guardians list.

Requirements:
- The caller must be the contract owner.
- `guardian` must be a guardian address.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| guardian | address | The address to be removed from the guardians list. |

### recoverERC20

```solidity
function recoverERC20(contract IERC20Upgradeable token, address recipient, uint256 amount) external
```

Allows the governance of the token contract to recover any ERC20
sent mistakenly to the token contract address.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| token | contract IERC20Upgradeable | The address of the token to be recovered. |
| recipient | address | The token recipient address that will receive recovered tokens. |
| amount | uint256 | The amount to be recovered. |

### recoverERC721

```solidity
function recoverERC721(contract IERC721Upgradeable token, address recipient, uint256 tokenId, bytes data) external
```

Allows the governance of the token contract to recover any
ERC721 sent mistakenly to the token contract address.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| token | contract IERC721Upgradeable | The address of the token to be recovered. |
| recipient | address | The token recipient address that will receive the recovered token. |
| tokenId | uint256 | The ID of the ERC721 token to be recovered. |
| data | bytes |  |

### pause

```solidity
function pause() external
```

Allows one of the guardians to pause mints and burns allowing
avoidance of contagion in case of a chain- or bridge-specific
incident.

Requirements:
- The caller must be a guardian.
- The contract must not be already paused.

### unpause

```solidity
function unpause() external
```

Allows the governance to unpause mints and burns previously
paused by one of the guardians.

Requirements:
- The caller must be the contract owner.
- The contract must be paused.

### mint

```solidity
function mint(address account, uint256 amount) external
```

Allows one of the minters to mint `amount` tokens and assign
them to `account`, increasing the total supply. Emits
a `Transfer` event with `from` set to the zero address.

Requirements:
- The caller must be a minter.
- `account` must not be the zero address.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| account | address | The address to receive tokens. |
| amount | uint256 | The amount of token to be minted. |

### burn

```solidity
function burn(uint256 amount) public
```

Destroys `amount` tokens from the caller. Emits a `Transfer`
event with `to` set to the zero address.

Requirements:
- The caller must have at least `amount` tokens.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | The amount of token to be burned. |

### burnFrom

```solidity
function burnFrom(address account, uint256 amount) public
```

Destroys `amount` tokens from `account`, deducting from the
caller's allowance. Emits a `Transfer` event with `to` set to
the zero address.

Requirements:
- The che caller must have allowance for `accounts`'s tokens of at
least `amount`.
- `account` must not be the zero address.
- `account` must have at least `amount` tokens.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| account | address | The address owning tokens to be burned. |
| amount | uint256 | The amount of token to be burned. |

### getMinters

```solidity
function getMinters() external view returns (address[])
```

Allows to fetch a list of all minters.

### getGuardians

```solidity
function getGuardians() external view returns (address[])
```

Allows to fetch a list of all guardians.

## IWormholeTokenBridge

Wormhole Token Bridge interface. Contains only selected functions
used by L2WormholeGateway.

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

## L2WormholeGateway

Selected cross-ecosystem bridges are given the minting authority for
tBTC token on L2 and sidechains. This contract gives a minting
authority to the Wormhole Bridge.

The process of bridging from L1 to L2 (or sidechain) looks as
follows:
1. There is a tBTC holder on L1. The holder goes to the Wormhole
Portal and selects the chain they want to bridge to.
2. The holder submits one transaction to L1 locking their tBTC
tokens in the bridge’s smart contract. After the transaction is
mined, they wait about 15 minutes for the Ethereum block
finality.
3. The holder submits one transaction to L2 that is minting tokens.
After that transaction is mined, they have their tBTC on L2.

The process of bridging from L2 (or sidechain) to L1 looks as
follows:
1. There is a tBTC holder on L2. That holder goes to the Wormhole
Portal and selects one of the L2 chains they want to bridge from.
2. The holder submits one transaction to L2 that is burning the
token. After the transaction is mined, they wait about 15 minutes
for the L2 block finality.
3. The holder submits one transaction to L1 unlocking their tBTC
tokens from the bridge’s smart contract. After that transaction
is mined, they have their tBTC on L1.

This smart contract is integrated with step 3 of L1->L2 bridging and
step 1 of L2->L1 or L2->L2 bridging. When the user redeems token on
L2, this contract receives the Wormhole tBTC representation and
mints the canonical tBTC in an equal amount. When user sends their
token from L1, this contract burns the canonical tBTC and sends
Wormhole tBTC representation through the bridge in an equal amount.

This contract is supposed to be deployed behind a transparent
upgradeable proxy.

### bridge

```solidity
contract IWormholeTokenBridge bridge
```

Reference to the Wormhole Token Bridge contract.

### bridgeToken

```solidity
contract IERC20Upgradeable bridgeToken
```

Wormhole tBTC token representation.

### tbtc

```solidity
contract L2TBTC tbtc
```

Canonical tBTC token.

### gateways

```solidity
mapping(uint16 => bytes32) gateways
```

Maps Wormhole chain ID to the Wormhole tBTC gateway address on
that chain. For example, this chain's ID should be mapped to
this contract's address. If there is no Wormhole tBTC gateway
address on the given chain, there is no entry in this mapping.
The mapping holds addresses in a Wormhole-specific format, where
Ethereum address is left-padded with zeros.

### mintingLimit

```solidity
uint256 mintingLimit
```

Minting limit for this gateway. Useful for early days of testing
the system. The gateway can not mint more canonical tBTC than
this limit.

### mintedAmount

```solidity
uint256 mintedAmount
```

The amount of tBTC minted by this contract. tBTC burned by this
contract decreases this amount.

### WormholeTbtcReceived

```solidity
event WormholeTbtcReceived(address receiver, uint256 amount)
```

### WormholeTbtcSent

```solidity
event WormholeTbtcSent(uint256 amount, uint16 recipientChain, bytes32 gateway, bytes32 recipient, uint256 arbiterFee, uint32 nonce)
```

### WormholeTbtcDeposited

```solidity
event WormholeTbtcDeposited(address depositor, uint256 amount)
```

### GatewayAddressUpdated

```solidity
event GatewayAddressUpdated(uint16 chainId, bytes32 gateway)
```

### MintingLimitUpdated

```solidity
event MintingLimitUpdated(uint256 mintingLimit)
```

### initialize

```solidity
function initialize(contract IWormholeTokenBridge _bridge, contract IERC20Upgradeable _bridgeToken, contract L2TBTC _tbtc) external
```

### sendTbtc

```solidity
function sendTbtc(uint256 amount, uint16 recipientChain, bytes32 recipient, uint256 arbiterFee, uint32 nonce) external payable returns (uint64)
```

This function is called when the user sends their token from L2.
The contract burns the canonical tBTC from the user and sends
wormhole tBTC representation over the bridge.
Keep in mind that when multiple bridges receive a minting
authority on the canonical tBTC, this function may not be able
to send all amounts of tBTC through the Wormhole bridge. The
capability of Wormhole Bridge to send tBTC from the chain is
limited to the amount of tBTC bridged through Wormhole to that
chain.

Requirements:
- The sender must have at least `amount` of the canonical tBTC and
it has to be approved for L2WormholeGateway.
- The L2WormholeGateway must have at least `amount` of the wormhole
tBTC.
- The recipient must not be 0x0.
- The amount to transfer must not be 0,
- The amount to transfer must be >= 10^10 (1e18 precision).
Depending if Wormhole tBTC gateway is registered on the target
chain, this function uses transfer or transfer with payload over
the Wormhole bridge.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | The amount of tBTC to be sent. |
| recipientChain | uint16 | The Wormhole recipient chain ID. |
| recipient | bytes32 | The address of the recipient in the Wormhole format. |
| arbiterFee | uint256 | The Wormhole arbiter fee. Ignored if sending tBTC to chain with Wormhole tBTC gateway. |
| nonce | uint32 | The Wormhole nonce used to batch messages together. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint64 | The Wormhole sequence number. |

### receiveTbtc

```solidity
function receiveTbtc(bytes encodedVm) external
```

This function is called when the user redeems their token on L2.
The contract receives Wormhole tBTC representation and mints the
canonical tBTC for the user.
If the tBTC minting limit has been reached by this contract,
instead of minting tBTC the receiver address receives Wormhole
tBTC representation.

Requirements:
- The receiver of Wormhole tBTC should be the L2WormholeGateway
contract.
- The receiver of the canonical tBTC should be abi-encoded in the
payload.
- The receiver of the canonical tBTC must not be the zero address.
The Wormhole Token Bridge contract has protection against redeeming
the same VAA again. When a Token Bridge VAA is redeemed, its
message body hash is stored in a map. This map is used to check
whether the hash has already been set in this map. For this reason,
this function does not have to be nonReentrant.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| encodedVm | bytes | A byte array containing a Wormhole VAA signed by the guardians. |

### depositWormholeTbtc

```solidity
function depositWormholeTbtc(uint256 amount) external
```

Allows to deposit Wormhole tBTC token in exchange for canonical
tBTC. Useful in a situation when user received wormhole tBTC
instead of canonical tBTC. One example of such situation is
when the minting limit was exceeded but the user minted anyway.

Requirements:
- The sender must have at least `amount` of the Wormhole tBTC and
it has to be approved for L2WormholeGateway.
- The minting limit must allow for minting the given amount.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | The amount of Wormhole tBTC to deposit. |

### updateGatewayAddress

```solidity
function updateGatewayAddress(uint16 chainId, bytes32 gateway) external
```

Lets the governance to update the tBTC gateway address on the
chain with the given Wormhole ID.

Use toWormholeAddress function to convert between Ethereum and
Wormhole address formats.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| chainId | uint16 | Wormhole ID of the chain. |
| gateway | bytes32 | Address of tBTC gateway on the given chain in a Wormhole format. |

### updateMintingLimit

```solidity
function updateMintingLimit(uint256 _mintingLimit) external
```

Lets the governance to update the tBTC minting limit for this
contract.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _mintingLimit | uint256 | The new minting limit. |

### toWormholeAddress

```solidity
function toWormholeAddress(address _address) external pure returns (bytes32)
```

Converts Ethereum address into Wormhole format.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _address | address | The address to convert. |

### fromWormholeAddress

```solidity
function fromWormholeAddress(bytes32 _address) public pure returns (address)
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

## MaintainerProxy

Maintainers are the willing off-chain clients approved by the governance.
Maintainers proxy calls to the `Bridge` contract via 'MaintainerProxy'
and are refunded for the spent gas from the `ReimbursementPool`.
There are two types of maintainers: wallet maintainers and SPV
maintainers.

### bridge

```solidity
contract Bridge bridge
```

### isWalletMaintainer

```solidity
mapping(address => uint256) isWalletMaintainer
```

Authorized wallet maintainers that can interact with the set of
functions for wallet maintainers only. Authorization can be
granted and removed by the governance.

'Key' is the address of the maintainer. 'Value' represents an index+1
in the 'maintainers' array. 1 was added so the maintainer index can
never be 0 which is a reserved index for a non-existent maintainer
in this map.

### walletMaintainers

```solidity
address[] walletMaintainers
```

This list of wallet maintainers keeps the order of which wallet
maintainer should be submitting a next transaction. It does not
enforce the order but only tracks who should be next in line.

### isSpvMaintainer

```solidity
mapping(address => uint256) isSpvMaintainer
```

Authorized SPV maintainers that can interact with the set of
functions for SPV maintainers only. Authorization can be
granted and removed by the governance.

'Key' is the address of the maintainer. 'Value' represents an index+1
in the 'maintainers' array. 1 was added so the maintainer index can
never be 0 which is a reserved index for a non-existent maintainer
in this map.

### spvMaintainers

```solidity
address[] spvMaintainers
```

This list of SPV maintainers keeps the order of which SPV
maintainer should be submitting a next transaction. It does not
enforce the order but only tracks who should be next in line.

### submitDepositSweepProofGasOffset

```solidity
uint256 submitDepositSweepProofGasOffset
```

Gas that is meant to balance the submission of deposit sweep proof
overall cost. Can be updated by the governance based on the current
market conditions.

### submitRedemptionProofGasOffset

```solidity
uint256 submitRedemptionProofGasOffset
```

Gas that is meant to balance the submission of redemption proof
overall cost. Can be updated by the governance based on the current
market conditions.

### resetMovingFundsTimeoutGasOffset

```solidity
uint256 resetMovingFundsTimeoutGasOffset
```

Gas that is meant to balance the reset of moving funds timeout
overall cost. Can be updated by the governance based on the current
market conditions.

### submitMovingFundsProofGasOffset

```solidity
uint256 submitMovingFundsProofGasOffset
```

Gas that is meant to balance the submission of moving funds proof
overall cost. Can be updated by the governance based on the current
market conditions.

### notifyMovingFundsBelowDustGasOffset

```solidity
uint256 notifyMovingFundsBelowDustGasOffset
```

Gas that is meant to balance the notification of moving funds below
dust overall cost. Can be updated by the governance based on the
current market conditions.

### submitMovedFundsSweepProofGasOffset

```solidity
uint256 submitMovedFundsSweepProofGasOffset
```

Gas that is meant to balance the submission of moved funds sweep
proof overall cost. Can be updated by the governance based on the
current market conditions.

### requestNewWalletGasOffset

```solidity
uint256 requestNewWalletGasOffset
```

Gas that is meant to balance the request of a new wallet overall
cost. Can be updated by the governance based on the current
market conditions.

### notifyWalletCloseableGasOffset

```solidity
uint256 notifyWalletCloseableGasOffset
```

Gas that is meant to balance the notification of closeable wallet
overall cost. Can be updated by the governance based on the current
market conditions.

### notifyWalletClosingPeriodElapsedGasOffset

```solidity
uint256 notifyWalletClosingPeriodElapsedGasOffset
```

Gas that is meant to balance the notification of wallet closing
period elapsed overall cost. Can be updated by the governance
based on the current market conditions.

### defeatFraudChallengeGasOffset

```solidity
uint256 defeatFraudChallengeGasOffset
```

Gas that is meant to balance the defeat fraud challenge
overall cost. Can be updated by the governance based on the current
market conditions.

### defeatFraudChallengeWithHeartbeatGasOffset

```solidity
uint256 defeatFraudChallengeWithHeartbeatGasOffset
```

Gas that is meant to balance the defeat fraud challenge with heartbeat
overall cost. Can be updated by the governance based on the current
market conditions.

### WalletMaintainerAuthorized

```solidity
event WalletMaintainerAuthorized(address maintainer)
```

### WalletMaintainerUnauthorized

```solidity
event WalletMaintainerUnauthorized(address maintainer)
```

### SpvMaintainerAuthorized

```solidity
event SpvMaintainerAuthorized(address maintainer)
```

### SpvMaintainerUnauthorized

```solidity
event SpvMaintainerUnauthorized(address maintainer)
```

### BridgeUpdated

```solidity
event BridgeUpdated(address newBridge)
```

### GasOffsetParametersUpdated

```solidity
event GasOffsetParametersUpdated(uint256 submitDepositSweepProofGasOffset, uint256 submitRedemptionProofGasOffset, uint256 resetMovingFundsTimeoutGasOffset, uint256 submitMovingFundsProofGasOffset, uint256 notifyMovingFundsBelowDustGasOffset, uint256 submitMovedFundsSweepProofGasOffset, uint256 requestNewWalletGasOffset, uint256 notifyWalletCloseableGasOffset, uint256 notifyWalletClosingPeriodElapsedGasOffset, uint256 defeatFraudChallengeGasOffset, uint256 defeatFraudChallengeWithHeartbeatGasOffset)
```

### onlyWalletMaintainer

```solidity
modifier onlyWalletMaintainer()
```

### onlySpvMaintainer

```solidity
modifier onlySpvMaintainer()
```

### onlyReimbursableAdmin

```solidity
modifier onlyReimbursableAdmin()
```

### constructor

```solidity
constructor(contract Bridge _bridge, contract ReimbursementPool _reimbursementPool) public
```

### submitDepositSweepProof

```solidity
function submitDepositSweepProof(struct BitcoinTx.Info sweepTx, struct BitcoinTx.Proof sweepProof, struct BitcoinTx.UTXO mainUtxo, address vault) external
```

Wraps `Bridge.submitDepositSweepProof` call and reimburses the
caller's transaction cost.

See `Bridge.submitDepositSweepProof` function documentation.

### submitRedemptionProof

```solidity
function submitRedemptionProof(struct BitcoinTx.Info redemptionTx, struct BitcoinTx.Proof redemptionProof, struct BitcoinTx.UTXO mainUtxo, bytes20 walletPubKeyHash) external
```

Wraps `Bridge.submitRedemptionProof` call and reimburses the
caller's transaction cost.

See `Bridge.submitRedemptionProof` function documentation.

### resetMovingFundsTimeout

```solidity
function resetMovingFundsTimeout(bytes20 walletPubKeyHash) external
```

Wraps `Bridge.resetMovingFundsTimeout` call and reimburses the
caller's transaction cost.

See `Bridge.resetMovingFundsTimeout` function documentation.

### submitMovingFundsProof

```solidity
function submitMovingFundsProof(struct BitcoinTx.Info movingFundsTx, struct BitcoinTx.Proof movingFundsProof, struct BitcoinTx.UTXO mainUtxo, bytes20 walletPubKeyHash) external
```

Wraps `Bridge.submitMovingFundsProof` call and reimburses the
caller's transaction cost.

See `Bridge.submitMovingFundsProof` function documentation.

### notifyMovingFundsBelowDust

```solidity
function notifyMovingFundsBelowDust(bytes20 walletPubKeyHash, struct BitcoinTx.UTXO mainUtxo) external
```

Wraps `Bridge.notifyMovingFundsBelowDust` call and reimburses the
caller's transaction cost.

See `Bridge.notifyMovingFundsBelowDust` function documentation.

### submitMovedFundsSweepProof

```solidity
function submitMovedFundsSweepProof(struct BitcoinTx.Info sweepTx, struct BitcoinTx.Proof sweepProof, struct BitcoinTx.UTXO mainUtxo) external
```

Wraps `Bridge.submitMovedFundsSweepProof` call and reimburses the
caller's transaction cost.

See `Bridge.submitMovedFundsSweepProof` function documentation.

### requestNewWallet

```solidity
function requestNewWallet(struct BitcoinTx.UTXO activeWalletMainUtxo) external
```

Wraps `Bridge.requestNewWallet` call and reimburses the
caller's transaction cost.

See `Bridge.requestNewWallet` function documentation.

### notifyWalletCloseable

```solidity
function notifyWalletCloseable(bytes20 walletPubKeyHash, struct BitcoinTx.UTXO walletMainUtxo) external
```

Wraps `Bridge.notifyWalletCloseable` call and reimburses the
caller's transaction cost.

See `Bridge.notifyWalletCloseable` function documentation.

### notifyWalletClosingPeriodElapsed

```solidity
function notifyWalletClosingPeriodElapsed(bytes20 walletPubKeyHash) external
```

Wraps `Bridge.notifyWalletClosingPeriodElapsed` call and reimburses
the caller's transaction cost.

See `Bridge.notifyWalletClosingPeriodElapsed` function documentation.

### defeatFraudChallenge

```solidity
function defeatFraudChallenge(bytes walletPublicKey, bytes preimage, bool witness) external
```

Wraps `Bridge.defeatFraudChallenge` call and reimburses the
caller's transaction cost.

See `Bridge.defeatFraudChallenge` function documentation.

### defeatFraudChallengeWithHeartbeat

```solidity
function defeatFraudChallengeWithHeartbeat(bytes walletPublicKey, bytes heartbeatMessage) external
```

Wraps `Bridge.defeatFraudChallengeWithHeartbeat` call and
reimburses the caller's transaction cost.

See `Bridge.defeatFraudChallengeWithHeartbeat` function documentation.

### authorizeWalletMaintainer

```solidity
function authorizeWalletMaintainer(address maintainer) external
```

Authorize a wallet maintainer that can interact with this
reimbursement pool. Can be authorized by the owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| maintainer | address | Wallet maintainer to authorize. |

### authorizeSpvMaintainer

```solidity
function authorizeSpvMaintainer(address maintainer) external
```

Authorize an SPV maintainer that can interact with this
reimbursement pool. Can be authorized by the owner only.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| maintainer | address | SPV maintainer to authorize. |

### unauthorizeWalletMaintainer

```solidity
function unauthorizeWalletMaintainer(address maintainerToUnauthorize) external
```

Unauthorize a wallet maintainer that was previously authorized to
interact with the Maintainer Proxy contract. Can be unauthorized
by the owner only.

The last maintainer is swapped with the one to be unauthorized.
The unauthorized maintainer is then removed from the list. An index
of the last maintainer is changed with the removed maintainer.
Ex.
'walletMaintainers' list: [0x1, 0x2, 0x3, 0x4, 0x5]
'isWalletMaintainer' map: [0x1 -> 1, 0x2 -> 2, 0x3 -> 3, 0x4 -> 4, 0x5 -> 5]
unauthorize: 0x3
new 'walletMaintainers' list: [0x1, 0x2, 0x5, 0x4]
new 'isWalletMaintainer' map: [0x1 -> 1, 0x2 -> 2, 0x4 -> 4, 0x5 -> 3]

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| maintainerToUnauthorize | address | Maintainer to unauthorize. |

### unauthorizeSpvMaintainer

```solidity
function unauthorizeSpvMaintainer(address maintainerToUnauthorize) external
```

Unauthorize an SPV maintainer that was previously authorized to
interact with the Maintainer Proxy contract. Can be unauthorized
by the owner only.

The last maintainer is swapped with the one to be unauthorized.
The unauthorized maintainer is then removed from the list. An index
of the last maintainer is changed with the removed maintainer.
Ex.
'spvMaintainers' list: [0x1, 0x2, 0x3, 0x4, 0x5]
'isSpvMaintainer' map: [0x1 -> 1, 0x2 -> 2, 0x3 -> 3, 0x4 -> 4, 0x5 -> 5]
unauthorize: 0x3
new 'spvMaintainers' list: [0x1, 0x2, 0x5, 0x4]
new 'isSpvMaintainer' map: [0x1 -> 1, 0x2 -> 2, 0x4 -> 4, 0x5 -> 3]

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| maintainerToUnauthorize | address | Maintainer to unauthorize. |

### updateBridge

```solidity
function updateBridge(contract Bridge _bridge) external
```

Allows the Governance to upgrade the Bridge address.

The function does not implement any governance delay and does not
check the status of the Bridge. The Governance implementation needs
to ensure all requirements for the upgrade are satisfied before
executing this function.

### updateGasOffsetParameters

```solidity
function updateGasOffsetParameters(uint256 newSubmitDepositSweepProofGasOffset, uint256 newSubmitRedemptionProofGasOffset, uint256 newResetMovingFundsTimeoutGasOffset, uint256 newSubmitMovingFundsProofGasOffset, uint256 newNotifyMovingFundsBelowDustGasOffset, uint256 newSubmitMovedFundsSweepProofGasOffset, uint256 newRequestNewWalletGasOffset, uint256 newNotifyWalletCloseableGasOffset, uint256 newNotifyWalletClosingPeriodElapsedGasOffset, uint256 newDefeatFraudChallengeGasOffset, uint256 newDefeatFraudChallengeWithHeartbeatGasOffset) external
```

Updates the values of gas offset parameters.

Can be called only by the contract owner. The caller is responsible
for validating parameters.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| newSubmitDepositSweepProofGasOffset | uint256 | New submit deposit sweep proof gas offset. |
| newSubmitRedemptionProofGasOffset | uint256 | New submit redemption proof gas offset. |
| newResetMovingFundsTimeoutGasOffset | uint256 | New reset moving funds timeout gas offset. |
| newSubmitMovingFundsProofGasOffset | uint256 | New submit moving funds proof gas offset. |
| newNotifyMovingFundsBelowDustGasOffset | uint256 | New notify moving funds below dust gas offset. |
| newSubmitMovedFundsSweepProofGasOffset | uint256 | New submit moved funds sweep proof gas offset. |
| newRequestNewWalletGasOffset | uint256 | New request new wallet gas offset. |
| newNotifyWalletCloseableGasOffset | uint256 | New notify closeable wallet gas offset. |
| newNotifyWalletClosingPeriodElapsedGasOffset | uint256 | New notify wallet closing period elapsed gas offset. |
| newDefeatFraudChallengeGasOffset | uint256 | New defeat fraud challenge gas offset. |
| newDefeatFraudChallengeWithHeartbeatGasOffset | uint256 | New defeat fraud challenge with heartbeat gas offset. |

### allWalletMaintainers

```solidity
function allWalletMaintainers() external view returns (address[])
```

Gets an entire array of wallet maintainer addresses.

### allSpvMaintainers

```solidity
function allSpvMaintainers() external view returns (address[])
```

Gets an entire array of SPV maintainer addresses.

## Epoch

```solidity
struct Epoch {
  uint32 timestamp;
  uint224 target;
}
```

## ILightRelay

### Genesis

```solidity
event Genesis(uint256 blockHeight)
```

### Retarget

```solidity
event Retarget(uint256 oldDifficulty, uint256 newDifficulty)
```

### ProofLengthChanged

```solidity
event ProofLengthChanged(uint256 newLength)
```

### AuthorizationRequirementChanged

```solidity
event AuthorizationRequirementChanged(bool newStatus)
```

### SubmitterAuthorized

```solidity
event SubmitterAuthorized(address submitter)
```

### SubmitterDeauthorized

```solidity
event SubmitterDeauthorized(address submitter)
```

### retarget

```solidity
function retarget(bytes headers) external
```

### validateChain

```solidity
function validateChain(bytes headers) external view returns (uint256 startingHeaderTimestamp, uint256 headerCount)
```

### getBlockDifficulty

```solidity
function getBlockDifficulty(uint256 blockNumber) external view returns (uint256)
```

### getEpochDifficulty

```solidity
function getEpochDifficulty(uint256 epochNumber) external view returns (uint256)
```

### getRelayRange

```solidity
function getRelayRange() external view returns (uint256 relayGenesis, uint256 currentEpochEnd)
```

## RelayUtils

### extractTimestampAt

```solidity
function extractTimestampAt(bytes headers, uint256 at) internal pure returns (uint32)
```

Extract the timestamp of the header at the given position.

Assumes that the specified position contains a valid header.
Performs no validation whatsoever.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| headers | bytes | Byte array containing the header of interest. |
| at | uint256 | The start of the header in the array. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint32 | The timestamp of the header. |

## LightRelay

THE RELAY MUST NOT BE USED BEFORE GENESIS AND AT LEAST ONE RETARGET.

### ready

```solidity
bool ready
```

### authorizationRequired

```solidity
bool authorizationRequired
```

### proofLength

```solidity
uint64 proofLength
```

### genesisEpoch

```solidity
uint64 genesisEpoch
```

### currentEpoch

```solidity
uint64 currentEpoch
```

### currentEpochDifficulty

```solidity
uint256 currentEpochDifficulty
```

### prevEpochDifficulty

```solidity
uint256 prevEpochDifficulty
```

### epochs

```solidity
mapping(uint256 => struct Epoch) epochs
```

### isAuthorized

```solidity
mapping(address => bool) isAuthorized
```

### relayActive

```solidity
modifier relayActive()
```

### genesis

```solidity
function genesis(bytes genesisHeader, uint256 genesisHeight, uint64 genesisProofLength) external
```

Establish a starting point for the relay by providing the
target, timestamp and blockheight of the first block of the relay
genesis epoch.

If the relay is used by querying the current and previous epoch
difficulty, at least one retarget needs to be provided after genesis;
otherwise the prevEpochDifficulty will be uninitialised and zero.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| genesisHeader | bytes | The first block header of the genesis epoch. |
| genesisHeight | uint256 | The block number of the first block of the epoch. |
| genesisProofLength | uint64 | The number of blocks required to accept a proof. |

### setProofLength

```solidity
function setProofLength(uint64 newLength) external
```

Set the number of blocks required to accept a header chain.

For production, a high number (e.g. 20-50) is recommended.
Small numbers are accepted but should only be used for testing.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| newLength | uint64 | The required number of blocks. Must be less than 2016. |

### setAuthorizationStatus

```solidity
function setAuthorizationStatus(bool status) external
```

Set whether the relay requires retarget submitters to be
pre-authorised by governance.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| status | bool | True if authorisation is to be required, false if not. |

### authorize

```solidity
function authorize(address submitter) external
```

Authorise the given address to submit retarget proofs.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| submitter | address | The address to be authorised. |

### deauthorize

```solidity
function deauthorize(address submitter) external
```

Rescind the authorisation of the submitter to retarget.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| submitter | address | The address to be deauthorised. |

### retarget

```solidity
function retarget(bytes headers) external
```

Add a new epoch to the relay by providing a proof
of the difficulty before and after the retarget.

Checks that the first X blocks are valid in the most recent epoch,
that the difficulty of the new epoch is calculated correctly according
to the block timestamps, and that the next X blocks would be valid in
the new epoch.
We have no information of block heights, so we cannot enforce that
retargets only happen every 2016 blocks; instead, we assume that this
is the case if a valid proof of work is provided.
It is possible to cheat the relay by providing X blocks from earlier in
the most recent epoch, and then mining X new blocks after them.
However, each of these malicious blocks would have to be mined to a
higher difficulty than the legitimate ones.
Alternatively, if the retarget has not been performed yet, one could
first mine X blocks in the old difficulty with timestamps set far in
the future, and then another X blocks at a greatly reduced difficulty.
In either case, cheating the realy requires more work than mining X
legitimate blocks.
Only the most recent epoch is vulnerable to these attacks; once a
retarget has been proven to the relay, the epoch is immutable even if a
contradictory proof were to be presented later.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| headers | bytes | A chain of headers including the last X blocks before the retarget, followed by the first X blocks after the retarget, where X equals the current proof length. |

### validateChain

```solidity
function validateChain(bytes headers) external view returns (uint256 startingHeaderTimestamp, uint256 headerCount)
```

Check whether a given chain of headers should be accepted as
valid within the rules of the relay.
If the validation fails, this function throws an exception.

A chain of headers is accepted as valid if:
- Its length is between 2 and 2015 headers.
- Headers in the chain are sequential and refer to previous digests.
- Each header is mined with the correct amount of work.
- The difficulty in each header matches an epoch of the relay,
as determined by the headers' timestamps. The headers must be between
the genesis epoch and the latest proven epoch (inclusive).
If the chain contains a retarget, it is accepted if the retarget has
already been proven to the relay.
If the chain contains blocks of an epoch that has not been proven to
the relay (after a retarget within the header chain, or when the entire
chain falls within an epoch that has not been proven yet), it will be
rejected.
One exception to this is when two subsequent epochs have exactly the
same difficulty; headers from the latter epoch will be accepted if the
previous epoch has been proven to the relay.
This is because it is not possible to distinguish such headers from
headers of the previous epoch.

If the difficulty increases significantly between relay genesis and the
present, creating fraudulent proofs for earlier epochs becomes easier.
Users of the relay should check the timestamps of valid headers and
only accept appropriately recent ones.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| headers | bytes | A chain of 2 to 2015 bitcoin headers. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| startingHeaderTimestamp | uint256 | The timestamp of the first header. |
| headerCount | uint256 | The number of headers. |

### getBlockDifficulty

```solidity
function getBlockDifficulty(uint256 blockNumber) external view returns (uint256)
```

Get the difficulty of the specified block.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| blockNumber | uint256 | The number of the block. Must fall within the relay range (at or after the relay genesis, and at or before the end of the most recent epoch proven to the relay). |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | The difficulty of the epoch. |

### getRelayRange

```solidity
function getRelayRange() external view returns (uint256 relayGenesis, uint256 currentEpochEnd)
```

Get the range of blocks the relay can accept proofs for.

Assumes that the genesis has been set correctly.
Additionally, if the next epoch after the current one has the exact
same difficulty, headers for it can be validated as well.
This function should be used for informative purposes,
e.g. to determine whether a retarget must be provided before submitting
a header chain for validation.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| relayGenesis | uint256 | The height of the earliest block that can be included in header chains for the relay to validate. |
| currentEpochEnd | uint256 | The height of the last block that can be included in header chains for the relay to validate. |

### getCurrentEpochDifficulty

```solidity
function getCurrentEpochDifficulty() external view virtual returns (uint256)
```

Returns the difficulty of the current epoch.

returns 0 if the relay is not ready.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | The difficulty of the current epoch. |

### getPrevEpochDifficulty

```solidity
function getPrevEpochDifficulty() external view virtual returns (uint256)
```

Returns the difficulty of the previous epoch.

Returns 0 if the relay is not ready or has not had a retarget.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | The difficulty of the previous epoch. |

### getCurrentAndPrevEpochDifficulty

```solidity
function getCurrentAndPrevEpochDifficulty() external view returns (uint256 current, uint256 previous)
```

### getEpochDifficulty

```solidity
function getEpochDifficulty(uint256 epochNumber) public view returns (uint256)
```

Get the difficulty of the specified epoch.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| epochNumber | uint256 | The number of the epoch (the height of the first block of the epoch, divided by 2016). Must fall within the relay range. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | The difficulty of the epoch. |

### validateHeader

```solidity
function validateHeader(bytes headers, uint256 start, bytes32 prevDigest) internal view returns (bytes32 digest, uint256 target)
```

Check that the specified header forms a correct chain with the
digest of the previous header (if provided), and has sufficient work.

Throws an exception if the header's chain or PoW are invalid.
Performs no other validation.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| headers | bytes | The byte array containing the header of interest. |
| start | uint256 | The start of the header in the array. |
| prevDigest | bytes32 | The digest of the previous header (optional; providing zeros for the digest skips the check). |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| digest | bytes32 | The digest of the current header. |
| target | uint256 | The PoW target of the header. |

## LightRelayMaintainerProxy

The proxy contract that allows the relay maintainers to be refunded
for the spent gas from the `ReimbursementPool`. When proving the
next Bitcoin difficulty epoch, the maintainer calls the
`LightRelayMaintainerProxy` which in turn calls the actual `LightRelay`
contract.

### lightRelay

```solidity
contract ILightRelay lightRelay
```

### isAuthorized

```solidity
mapping(address => bool) isAuthorized
```

Stores the addresses that can maintain the relay. Those
addresses are attested by the DAO.

The goal is to prevent a griefing attack by frontrunning relay
maintainer which is responsible for retargetting the relay in the
given round. The maintainer's transaction would revert with no gas
refund. Having the ability to restrict maintainer addresses is also
important in case the underlying relay contract has authorization
requirements for callers.

### retargetGasOffset

```solidity
uint256 retargetGasOffset
```

Gas that is meant to balance the retarget overall cost. Can be

### LightRelayUpdated

```solidity
event LightRelayUpdated(address newRelay)
```

### MaintainerAuthorized

```solidity
event MaintainerAuthorized(address maintainer)
```

### MaintainerDeauthorized

```solidity
event MaintainerDeauthorized(address maintainer)
```

### RetargetGasOffsetUpdated

```solidity
event RetargetGasOffsetUpdated(uint256 retargetGasOffset)
```

### onlyRelayMaintainer

```solidity
modifier onlyRelayMaintainer()
```

### onlyReimbursableAdmin

```solidity
modifier onlyReimbursableAdmin()
```

### constructor

```solidity
constructor(contract ILightRelay _lightRelay, contract ReimbursementPool _reimbursementPool) public
```

### updateLightRelay

```solidity
function updateLightRelay(contract ILightRelay _lightRelay) external
```

Allows the governance to upgrade the `LightRelay` address.

The function does not implement any governance delay and does not
check the status of the `LightRelay`. The Governance implementation
needs to ensure all requirements for the upgrade are satisfied
before executing this function.

### authorize

```solidity
function authorize(address maintainer) external
```

Authorizes the given address as a maintainer. Can only be called
by the owner and the address of the maintainer must not be
already authorized.

The function does not implement any governance delay.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| maintainer | address | The address of the maintainer to be authorized. |

### deauthorize

```solidity
function deauthorize(address maintainer) external
```

Deauthorizes the given address as a maintainer. Can only be
called by the owner and the address of the maintainer must be
authorized.

The function does not implement any governance delay.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| maintainer | address | The address of the maintainer to be deauthorized. |

### updateRetargetGasOffset

```solidity
function updateRetargetGasOffset(uint256 newRetargetGasOffset) external
```

Updates the values of retarget gas offset.

Can be called only by the contract owner. The caller is responsible
for validating the parameter. The function does not implement any
governance delay.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| newRetargetGasOffset | uint256 | New retarget gas offset. |

### retarget

```solidity
function retarget(bytes headers) external
```

Wraps `LightRelay.retarget` call and reimburses the caller's
transaction cost. Can only be called by an authorized relay
maintainer.

See `LightRelay.retarget` function documentation.

## TBTC

### constructor

```solidity
constructor() public
```

## DonationVault

Vault that allows making BTC donations to the system. Upon deposit,
this vault does not increase depositors' balances and always
decreases its own balance in the same transaction. The vault also
allows making donations using existing Bank balances.

BEWARE: ALL BTC DEPOSITS TARGETING THIS VAULT ARE NOT REDEEMABLE
AND THERE IS NO WAY TO RESTORE THE DONATED BALANCE.
USE THIS VAULT ONLY WHEN YOU REALLY KNOW WHAT YOU ARE DOING!

### bank

```solidity
contract Bank bank
```

### DonationReceived

```solidity
event DonationReceived(address donor, uint256 donatedAmount)
```

### onlyBank

```solidity
modifier onlyBank()
```

### constructor

```solidity
constructor(contract Bank _bank) public
```

### donate

```solidity
function donate(uint256 amount) external
```

Transfers the given `amount` of the Bank balance from the
caller to the Donation Vault and immediately decreases the
vault's balance in the Bank by the transferred `amount`.

Requirements:
- The caller's balance in the Bank must be greater than or equal
to the `amount`,
- Donation Vault must have an allowance for caller's balance in
the Bank for at least `amount`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | Amount of the Bank balance to donate. |

### receiveBalanceApproval

```solidity
function receiveBalanceApproval(address owner, uint256 amount, bytes) external
```

Transfers the given `amount` of the Bank balance from the
`owner` to the Donation Vault and immediately decreases the
vault's balance in the Bank by the transferred `amount`.

Requirements:
- Can only be called by the Bank via `approveBalanceAndCall`,
- The `owner` balance in the Bank must be greater than or equal
to the `amount`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| owner | address | Address of the Bank balance owner who approved their balance to be used by the vault. |
| amount | uint256 | The amount of the Bank balance approved by the owner to be used by the vault. |
|  | bytes |  |

### receiveBalanceIncrease

```solidity
function receiveBalanceIncrease(address[] depositors, uint256[] depositedAmounts) external
```

Ignores the deposited amounts and does not increase depositors'
individual balances. The vault decreases its own tBTC balance
in the Bank by the total deposited amount.

Requirements:
- Can only be called by the Bank after the Bridge swept deposits
and Bank increased balance for the vault,
- The `depositors` array must not be empty,
- The `depositors` array length must be equal to the
`depositedAmounts` array length.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| depositors | address[] | Addresses of depositors whose deposits have been swept. |
| depositedAmounts | uint256[] | Amounts deposited by individual depositors and swept. |

## TBTCOptimisticMinting

The Optimistic Minting mechanism allows to mint TBTC before
`TBTCVault` receives the Bank balance. There are two permissioned
sets in the system: Minters and Guardians, both set up in 1-of-n
mode. Minters observe the revealed deposits and request minting TBTC.
Any single Minter can perform this action. There is an
`optimisticMintingDelay` between the time of the request from
a Minter to the time TBTC is minted. During the time of the delay,
any Guardian can cancel the minting.

This functionality is a part of `TBTCVault`. It is implemented in
a separate abstract contract to achieve better separation of concerns
and easier-to-follow code.

### OptimisticMintingRequest

```solidity
struct OptimisticMintingRequest {
  uint64 requestedAt;
  uint64 finalizedAt;
}
```

### GOVERNANCE_DELAY

```solidity
uint256 GOVERNANCE_DELAY
```

The time delay that needs to pass between initializing and
finalizing the upgrade of governable parameters.

### SATOSHI_MULTIPLIER

```solidity
uint256 SATOSHI_MULTIPLIER
```

Multiplier to convert satoshi to TBTC token units.

### bridge

```solidity
contract Bridge bridge
```

### isOptimisticMintingPaused

```solidity
bool isOptimisticMintingPaused
```

Indicates if the optimistic minting has been paused. Only the
Governance can pause optimistic minting. Note that the pause of
the optimistic minting does not stop the standard minting flow
where wallets sweep deposits.

### optimisticMintingFeeDivisor

```solidity
uint32 optimisticMintingFeeDivisor
```

Divisor used to compute the treasury fee taken from each
optimistically minted deposit and transferred to the treasury
upon finalization of the optimistic mint. This fee is computed
as follows: `fee = amount / optimisticMintingFeeDivisor`.
For example, if the fee needs to be 2%, the
`optimisticMintingFeeDivisor` should be set to `50` because
`1/50 = 0.02 = 2%`.
The optimistic minting fee does not replace the deposit treasury
fee cut by the Bridge. The optimistic fee is a percentage AFTER
the treasury fee is cut:
`optimisticMintingFee = (depositAmount - treasuryFee) / optimisticMintingFeeDivisor`

### optimisticMintingDelay

```solidity
uint32 optimisticMintingDelay
```

The time that needs to pass between the moment the optimistic
minting is requested and the moment optimistic minting is
finalized with minting TBTC.

### isMinter

```solidity
mapping(address => bool) isMinter
```

Indicates if the given address is a Minter. Only Minters can
request optimistic minting.

### minters

```solidity
address[] minters
```

List of all Minters.

May be used to establish an order in which the Minters should
request for an optimistic minting.

### isGuardian

```solidity
mapping(address => bool) isGuardian
```

Indicates if the given address is a Guardian. Only Guardians can
cancel requested optimistic minting.

### optimisticMintingRequests

```solidity
mapping(uint256 => struct TBTCOptimisticMinting.OptimisticMintingRequest) optimisticMintingRequests
```

Collection of all revealed deposits for which the optimistic
minting was requested. Indexed by a deposit key computed as
`keccak256(fundingTxHash | fundingOutputIndex)`.

### optimisticMintingDebt

```solidity
mapping(address => uint256) optimisticMintingDebt
```

Optimistic minting debt value per depositor's address. The debt
represents the total value of all depositor's deposits revealed
to the Bridge that has not been yet swept and led to the
optimistic minting of TBTC. When `TBTCVault` sweeps a deposit,
the debt is fully or partially paid off, no matter if that
particular swept deposit was used for the optimistic minting or
not. The values are in 1e18 Ethereum precision.

### newOptimisticMintingFeeDivisor

```solidity
uint32 newOptimisticMintingFeeDivisor
```

New optimistic minting fee divisor value. Set only when the
parameter update process is pending. Once the update gets

### optimisticMintingFeeUpdateInitiatedTimestamp

```solidity
uint256 optimisticMintingFeeUpdateInitiatedTimestamp
```

The timestamp at which the update of the optimistic minting fee
divisor started. Zero if update is not in progress.

### newOptimisticMintingDelay

```solidity
uint32 newOptimisticMintingDelay
```

New optimistic minting delay value. Set only when the parameter
update process is pending. Once the update gets finalized, this

### optimisticMintingDelayUpdateInitiatedTimestamp

```solidity
uint256 optimisticMintingDelayUpdateInitiatedTimestamp
```

The timestamp at which the update of the optimistic minting
delay started. Zero if update is not in progress.

### OptimisticMintingRequested

```solidity
event OptimisticMintingRequested(address minter, uint256 depositKey, address depositor, uint256 amount, bytes32 fundingTxHash, uint32 fundingOutputIndex)
```

### OptimisticMintingFinalized

```solidity
event OptimisticMintingFinalized(address minter, uint256 depositKey, address depositor, uint256 optimisticMintingDebt)
```

### OptimisticMintingCancelled

```solidity
event OptimisticMintingCancelled(address guardian, uint256 depositKey)
```

### OptimisticMintingDebtRepaid

```solidity
event OptimisticMintingDebtRepaid(address depositor, uint256 optimisticMintingDebt)
```

### MinterAdded

```solidity
event MinterAdded(address minter)
```

### MinterRemoved

```solidity
event MinterRemoved(address minter)
```

### GuardianAdded

```solidity
event GuardianAdded(address guardian)
```

### GuardianRemoved

```solidity
event GuardianRemoved(address guardian)
```

### OptimisticMintingPaused

```solidity
event OptimisticMintingPaused()
```

### OptimisticMintingUnpaused

```solidity
event OptimisticMintingUnpaused()
```

### OptimisticMintingFeeUpdateStarted

```solidity
event OptimisticMintingFeeUpdateStarted(uint32 newOptimisticMintingFeeDivisor)
```

### OptimisticMintingFeeUpdated

```solidity
event OptimisticMintingFeeUpdated(uint32 newOptimisticMintingFeeDivisor)
```

### OptimisticMintingDelayUpdateStarted

```solidity
event OptimisticMintingDelayUpdateStarted(uint32 newOptimisticMintingDelay)
```

### OptimisticMintingDelayUpdated

```solidity
event OptimisticMintingDelayUpdated(uint32 newOptimisticMintingDelay)
```

### onlyMinter

```solidity
modifier onlyMinter()
```

### onlyGuardian

```solidity
modifier onlyGuardian()
```

### onlyOwnerOrGuardian

```solidity
modifier onlyOwnerOrGuardian()
```

### whenOptimisticMintingNotPaused

```solidity
modifier whenOptimisticMintingNotPaused()
```

### onlyAfterGovernanceDelay

```solidity
modifier onlyAfterGovernanceDelay(uint256 updateInitiatedTimestamp)
```

### constructor

```solidity
constructor(contract Bridge _bridge) internal
```

### _mint

```solidity
function _mint(address minter, uint256 amount) internal virtual
```

Mints the given amount of TBTC to the given depositor's address.
Implemented by TBTCVault.

### getMinters

```solidity
function getMinters() external view returns (address[])
```

Allows to fetch a list of all Minters.

### requestOptimisticMint

```solidity
function requestOptimisticMint(bytes32 fundingTxHash, uint32 fundingOutputIndex) external
```

Allows a Minter to request for an optimistic minting of TBTC.
The following conditions must be met:
- There is no optimistic minting request for the deposit,
finalized or not.
- The deposit with the given Bitcoin funding transaction hash
and output index has been revealed to the Bridge.
- The deposit has not been swept yet.
- The deposit is targeted into the TBTCVault.
- The optimistic minting is not paused.
After calling this function, the Minter has to wait for
`optimisticMintingDelay` before finalizing the mint with a call
to finalizeOptimisticMint.

The deposit done on the Bitcoin side must be revealed early enough
to the Bridge on Ethereum to pass the Bridge's validation. The
validation passes successfully only if the deposit reveal is done
respectively earlier than the moment when the deposit refund
locktime is reached, i.e. the deposit becomes refundable. It may
happen that the wallet does not sweep a revealed deposit and one of
the Minters requests an optimistic mint for that deposit just
before the locktime is reached. Guardians must cancel optimistic
minting for this deposit because the wallet will not be able to
sweep it. The on-chain optimistic minting code does not perform any
validation for gas efficiency: it would have to perform the same
validation as `validateDepositRefundLocktime` and expect the entire
`DepositRevealInfo` to be passed to assemble the expected script
hash on-chain. Guardians must validate if the deposit happened on
Bitcoin, that the script hash has the expected format, and that the
wallet is an active one so they can also validate the time left for
the refund.

### finalizeOptimisticMint

```solidity
function finalizeOptimisticMint(bytes32 fundingTxHash, uint32 fundingOutputIndex) external
```

Allows a Minter to finalize previously requested optimistic
minting. The following conditions must be met:
- The optimistic minting has been requested for the given
deposit.
- The deposit has not been swept yet.
- At least `optimisticMintingDelay` passed since the optimistic
minting was requested for the given deposit.
- The optimistic minting has not been finalized earlier for the
given deposit.
- The optimistic minting request for the given deposit has not
been canceled by a Guardian.
- The optimistic minting is not paused.
This function mints TBTC and increases `optimisticMintingDebt`
for the given depositor. The optimistic minting request is
marked as finalized.

### cancelOptimisticMint

```solidity
function cancelOptimisticMint(bytes32 fundingTxHash, uint32 fundingOutputIndex) external
```

Allows a Guardian to cancel optimistic minting request. The
following conditions must be met:
- The optimistic minting request for the given deposit exists.
- The optimistic minting request for the given deposit has not
been finalized yet.
Optimistic minting request is removed. It is possible to request
optimistic minting again for the same deposit later.

Guardians must validate the following conditions for every deposit
for which the optimistic minting was requested:
- The deposit happened on Bitcoin side and it has enough
confirmations.
- The optimistic minting has been requested early enough so that
the wallet has enough time to sweep the deposit.
- The wallet is an active one and it does perform sweeps or it will
perform sweeps once the sweeps are activated.

### addMinter

```solidity
function addMinter(address minter) external
```

Adds the address to the Minter list.

### removeMinter

```solidity
function removeMinter(address minter) external
```

Removes the address from the Minter list.

### addGuardian

```solidity
function addGuardian(address guardian) external
```

Adds the address to the Guardian set.

### removeGuardian

```solidity
function removeGuardian(address guardian) external
```

Removes the address from the Guardian set.

### pauseOptimisticMinting

```solidity
function pauseOptimisticMinting() external
```

Pauses the optimistic minting. Note that the pause of the
optimistic minting does not stop the standard minting flow
where wallets sweep deposits.

### unpauseOptimisticMinting

```solidity
function unpauseOptimisticMinting() external
```

Unpauses the optimistic minting.

### beginOptimisticMintingFeeUpdate

```solidity
function beginOptimisticMintingFeeUpdate(uint32 _newOptimisticMintingFeeDivisor) external
```

Begins the process of updating optimistic minting fee.
The fee is computed as follows:
`fee = amount / optimisticMintingFeeDivisor`.
For example, if the fee needs to be 2% of each deposit,
the `optimisticMintingFeeDivisor` should be set to `50` because
`1/50 = 0.02 = 2%`.

See the documentation for optimisticMintingFeeDivisor.

### finalizeOptimisticMintingFeeUpdate

```solidity
function finalizeOptimisticMintingFeeUpdate() external
```

Finalizes the update process of the optimistic minting fee.

### beginOptimisticMintingDelayUpdate

```solidity
function beginOptimisticMintingDelayUpdate(uint32 _newOptimisticMintingDelay) external
```

Begins the process of updating optimistic minting delay.

### finalizeOptimisticMintingDelayUpdate

```solidity
function finalizeOptimisticMintingDelayUpdate() external
```

Finalizes the update process of the optimistic minting delay.

### calculateDepositKey

```solidity
function calculateDepositKey(bytes32 fundingTxHash, uint32 fundingOutputIndex) public pure returns (uint256)
```

Calculates deposit key the same way as the Bridge contract.
The deposit key is computed as
`keccak256(fundingTxHash | fundingOutputIndex)`.

### repayOptimisticMintingDebt

```solidity
function repayOptimisticMintingDebt(address depositor, uint256 amount) internal returns (uint256)
```

Used by `TBTCVault.receiveBalanceIncrease` to repay the optimistic
minting debt before TBTC is minted. When optimistic minting is
finalized, debt equal to the value of the deposit being
a subject of the optimistic minting is incurred. When `TBTCVault`
sweeps a deposit, the debt is fully or partially paid off, no
matter if that particular deposit was used for the optimistic
minting or not.

See `TBTCVault.receiveBalanceIncrease`

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| depositor | address | The depositor whose balance increase is received. |
| amount | uint256 | The balance increase amount for the depositor received. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | The TBTC amount that should be minted after paying off the optimistic minting debt. |

## TBTCVault

TBTC is a fully Bitcoin-backed ERC-20 token pegged to the price of
Bitcoin. It facilitates Bitcoin holders to act on the Ethereum
blockchain and access the decentralized finance (DeFi) ecosystem.
TBTC Vault mints and unmints TBTC based on Bitcoin balances in the
Bank.

TBTC Vault is the owner of TBTC token contract and is the only contract
minting the token.

### bank

```solidity
contract Bank bank
```

### tbtcToken

```solidity
contract TBTC tbtcToken
```

### newVault

```solidity
address newVault
```

The address of a new TBTC vault. Set only when the upgrade
process is pending. Once the upgrade gets finalized, the new
TBTC vault will become an owner of TBTC token.

### upgradeInitiatedTimestamp

```solidity
uint256 upgradeInitiatedTimestamp
```

The timestamp at which an upgrade to a new TBTC vault was
initiated. Set only when the upgrade process is pending.

### Minted

```solidity
event Minted(address to, uint256 amount)
```

### Unminted

```solidity
event Unminted(address from, uint256 amount)
```

### UpgradeInitiated

```solidity
event UpgradeInitiated(address newVault, uint256 timestamp)
```

### UpgradeFinalized

```solidity
event UpgradeFinalized(address newVault)
```

### onlyBank

```solidity
modifier onlyBank()
```

### constructor

```solidity
constructor(contract Bank _bank, contract TBTC _tbtcToken, contract Bridge _bridge) public
```

### mint

```solidity
function mint(uint256 amount) external
```

Mints the given `amount` of TBTC to the caller previously
transferring `amount / SATOSHI_MULTIPLIER` of the Bank balance
from caller to TBTC Vault. If `amount` is not divisible by
SATOSHI_MULTIPLIER, the remainder is left on the caller's
Bank balance.

TBTC Vault must have an allowance for caller's balance in the
Bank for at least `amount / SATOSHI_MULTIPLIER`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | Amount of TBTC to mint. |

### receiveBalanceApproval

```solidity
function receiveBalanceApproval(address owner, uint256 satoshis, bytes) external
```

Transfers `satoshis` of the Bank balance from the caller
to TBTC Vault and mints `satoshis * SATOSHI_MULTIPLIER` of TBTC
to the caller.

Can only be called by the Bank via `approveBalanceAndCall`.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| owner | address | The owner who approved their Bank balance. |
| satoshis | uint256 | Amount of satoshis used to mint TBTC. |
|  | bytes |  |

### receiveBalanceIncrease

```solidity
function receiveBalanceIncrease(address[] depositors, uint256[] depositedSatoshiAmounts) external
```

Mints the same amount of TBTC as the deposited satoshis amount
multiplied by SATOSHI_MULTIPLIER for each depositor in the array.
Can only be called by the Bank after the Bridge swept deposits
and Bank increased balance for the vault.

Fails if `depositors` array is empty. Expects the length of
`depositors` and `depositedSatoshiAmounts` is the same.

### unmint

```solidity
function unmint(uint256 amount) external
```

Burns `amount` of TBTC from the caller's balance and transfers
`amount / SATOSHI_MULTIPLIER` back to the caller's balance in
the Bank. If `amount` is not divisible by SATOSHI_MULTIPLIER,
the remainder is left on the caller's account.

Caller must have at least `amount` of TBTC approved to
TBTC Vault.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | Amount of TBTC to unmint. |

### unmintAndRedeem

```solidity
function unmintAndRedeem(uint256 amount, bytes redemptionData) external
```

Burns `amount` of TBTC from the caller's balance and transfers
`amount / SATOSHI_MULTIPLIER` of Bank balance to the Bridge
requesting redemption based on the provided `redemptionData`.
If `amount` is not divisible by SATOSHI_MULTIPLIER, the
remainder is left on the caller's account.

Caller must have at least `amount` of TBTC approved to
TBTC Vault.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | Amount of TBTC to unmint and request to redeem in Bridge. |
| redemptionData | bytes | Redemption data in a format expected from `redemptionData` parameter of Bridge's `receiveBalanceApproval` function. |

### receiveApproval

```solidity
function receiveApproval(address from, uint256 amount, address token, bytes extraData) external
```

Burns `amount` of TBTC from the caller's balance. If `extraData`
is empty, transfers `amount` back to the caller's balance in the
Bank. If `extraData` is not empty, requests redemption in the
Bridge using the `extraData` as a `redemptionData` parameter to
Bridge's `receiveBalanceApproval` function.
If `amount` is not divisible by SATOSHI_MULTIPLIER, the
remainder is left on the caller's account. Note that it may
left a token approval equal to the remainder.

This function is doing the same as `unmint` or `unmintAndRedeem`
(depending on `extraData` parameter) but it allows to execute
unminting without a separate approval transaction. The function can
be called only via `approveAndCall` of TBTC token.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| from | address | TBTC token holder executing unminting. |
| amount | uint256 | Amount of TBTC to unmint. |
| token | address | TBTC token address. |
| extraData | bytes | Redemption data in a format expected from `redemptionData` parameter of Bridge's `receiveBalanceApproval` function. If empty, `receiveApproval` is not requesting a redemption of Bank balance but is instead performing just TBTC unminting to a Bank balance. |

### initiateUpgrade

```solidity
function initiateUpgrade(address _newVault) external
```

Initiates vault upgrade process. The upgrade process needs to be
finalized with a call to `finalizeUpgrade` function after the
`UPGRADE_GOVERNANCE_DELAY` passes. Only the governance can
initiate the upgrade.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _newVault | address | The new vault address. |

### finalizeUpgrade

```solidity
function finalizeUpgrade() external
```

Allows the governance to finalize vault upgrade process. The
upgrade process needs to be first initiated with a call to
`initiateUpgrade` and the `GOVERNANCE_DELAY` needs to pass.
Once the upgrade is finalized, the new vault becomes the owner
of the TBTC token and receives the whole Bank balance of this
vault.

### recoverERC20FromToken

```solidity
function recoverERC20FromToken(contract IERC20 token, address recipient, uint256 amount) external
```

Allows the governance of the TBTCVault to recover any ERC20
token sent mistakenly to the TBTC token contract address.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| token | contract IERC20 | Address of the recovered ERC20 token contract. |
| recipient | address | Address the recovered token should be sent to. |
| amount | uint256 | Recovered amount. |

### recoverERC721FromToken

```solidity
function recoverERC721FromToken(contract IERC721 token, address recipient, uint256 tokenId, bytes data) external
```

Allows the governance of the TBTCVault to recover any ERC721
token sent mistakenly to the TBTC token contract address.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| token | contract IERC721 | Address of the recovered ERC721 token contract. |
| recipient | address | Address the recovered token should be sent to. |
| tokenId | uint256 | Identifier of the recovered token. |
| data | bytes | Additional data. |

### recoverERC20

```solidity
function recoverERC20(contract IERC20 token, address recipient, uint256 amount) external
```

Allows the governance of the TBTCVault to recover any ERC20
token sent - mistakenly or not - to the vault address. This
function should be used to withdraw TBTC v1 tokens transferred
to TBTCVault as a result of VendingMachine > TBTCVault upgrade.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| token | contract IERC20 | Address of the recovered ERC20 token contract. |
| recipient | address | Address the recovered token should be sent to. |
| amount | uint256 | Recovered amount. |

### recoverERC721

```solidity
function recoverERC721(contract IERC721 token, address recipient, uint256 tokenId, bytes data) external
```

Allows the governance of the TBTCVault to recover any ERC721
token sent mistakenly to the vault address.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| token | contract IERC721 | Address of the recovered ERC721 token contract. |
| recipient | address | Address the recovered token should be sent to. |
| tokenId | uint256 | Identifier of the recovered token. |
| data | bytes | Additional data. |

### amountToSatoshis

```solidity
function amountToSatoshis(uint256 amount) public view returns (uint256 convertibleAmount, uint256 remainder, uint256 satoshis)
```

Returns the amount of TBTC to be minted/unminted, the remainder,
and the Bank balance to be transferred for the given mint/unmint.
Note that if the `amount` is not divisible by SATOSHI_MULTIPLIER,
the remainder is left on the caller's account when minting or
unminting.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| convertibleAmount | uint256 | Amount of TBTC to be minted/unminted. |
| remainder | uint256 | Not convertible remainder if amount is not divisible by SATOSHI_MULTIPLIER. |
| satoshis | uint256 | Amount in satoshis - the Bank balance to be transferred for the given mint/unmint |

### _mint

```solidity
function _mint(address minter, uint256 amount) internal
```

Mints the given amount of TBTC to the given depositor's address.
Implemented by TBTCVault.

### _unmint

```solidity
function _unmint(address unminter, uint256 amount) internal
```

`amount` MUST be divisible by SATOSHI_MULTIPLIER with no change.

### _unmintAndRedeem

```solidity
function _unmintAndRedeem(address redeemer, uint256 amount, bytes redemptionData) internal
```

`amount` MUST be divisible by SATOSHI_MULTIPLIER with no change.

