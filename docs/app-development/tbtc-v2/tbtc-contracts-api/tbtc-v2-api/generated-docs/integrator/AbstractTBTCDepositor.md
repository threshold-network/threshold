# Solidity API

## AbstractTBTCDepositor

}
}

### SATOSHI_MULTIPLIER

```solidity
uint256 SATOSHI_MULTIPLIER
```

Multiplier to convert satoshi to TBTC token units.

### bridge

```solidity
contract IBridge bridge
```

Bridge contract address.

### tbtcVault

```solidity
contract ITBTCVault tbtcVault
```

TBTCVault contract address.

### __AbstractTBTCDepositor_initialize

```solidity
function __AbstractTBTCDepositor_initialize(address _bridge, address _tbtcVault) internal
```

Initializes the contract. MUST BE CALLED from the child
contract initializer.

### _initializeDeposit

```solidity
function _initializeDeposit(struct IBridgeTypes.BitcoinTxInfo fundingTx, struct IBridgeTypes.DepositRevealInfo reveal, bytes32 extraData) internal returns (uint256 depositKey, uint256 initialDepositAmount)
```

Initializes a deposit by revealing it to the Bridge.

Requirements:
- The revealed vault address must match the TBTCVault address,
- All requirements from {Bridge#revealDepositWithExtraData}
function must be met.
This function doesn't validate if a deposit has been initialized before,
as the Bridge won't allow the same deposit to be revealed twice.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| fundingTx | struct IBridgeTypes.BitcoinTxInfo | Bitcoin funding transaction data, see `IBridgeTypes.BitcoinTxInfo`. |
| reveal | struct IBridgeTypes.DepositRevealInfo | Deposit reveal data, see `IBridgeTypes.DepositRevealInfo` struct. |
| extraData | bytes32 | 32-byte deposit extra data. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| depositKey | uint256 | Deposit key computed as `keccak256(fundingTxHash | reveal.fundingOutputIndex)`. This key can be used to refer to the deposit in the Bridge and TBTCVault contracts. |
| initialDepositAmount | uint256 | Amount of funding transaction deposit. In TBTC token decimals precision. |

### _finalizeDeposit

```solidity
function _finalizeDeposit(uint256 depositKey) internal returns (uint256 initialDepositAmount, uint256 tbtcAmount, bytes32 extraData)
```

Finalizes a deposit by calculating the amount of TBTC minted
for the deposit.

Requirements:
- The deposit must be initialized but not finalized
(in the context of this contract) yet.
- The deposit must be finalized on the Bridge side. That means the
deposit must be either swept or optimistically minted.
THIS FUNCTION DOESN'T VALIDATE IF A DEPOSIT HAS BEEN FINALIZED BEFORE,
IT IS A RESPONSIBILITY OF THE IMPLEMENTING CONTRACT TO ENSURE THIS
FUNCTION WON'T BE CALLED TWICE FOR THE SAME DEPOSIT.
IMPORTANT NOTE: The tbtcAmount returned by this function is an
approximation. See documentation of the `calculateTbtcAmount`
responsible for calculating this value for more details.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| depositKey | uint256 | Deposit key identifying the deposit. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| initialDepositAmount | uint256 | Amount of funding transaction deposit. In TBTC token decimals precision. |
| tbtcAmount | uint256 | Approximate amount of TBTC minted for the deposit. In TBTC token decimals precision. |
| extraData | bytes32 | 32-byte deposit extra data. |

### _calculateTbtcAmount

```solidity
function _calculateTbtcAmount(uint64 depositAmountSat, uint64 depositTreasuryFeeSat) internal view virtual returns (uint256)
```

Calculates the amount of TBTC minted for the deposit.

IMPORTANT NOTE: The tbtcAmount returned by this function may
not correspond to the actual amount of TBTC minted for the deposit.
Although the treasury fee cut upon minting is known precisely,
this is not the case for the optimistic minting fee and the Bitcoin
transaction fee. To overcome that problem, this function just takes
the current maximum allowed values of both fees, at the moment of deposit
finalization. For the great majority of the deposits, such an
algorithm will return a tbtcAmount slightly lesser than the
actual amount of TBTC minted for the deposit. This will cause
some TBTC to be left in the contract and ensure there is enough
liquidity to finalize the deposit. However, in some rare cases,
where the actual values of those fees change between the deposit
minting and finalization, the tbtcAmount returned by this function
may be greater than the actual amount of TBTC minted for the deposit.
If this happens and the reserve coming from previous deposits
leftovers does not provide enough liquidity, the deposit will have
to wait for finalization until the reserve is refilled by subsequent
deposits or a manual top-up. The integrator is responsible for
handling such cases.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| depositAmountSat | uint64 | Deposit amount in satoshi (1e8 precision). This is the actual amount deposited by the deposit creator, i.e. the gross amount the Bridge's fees are cut from. |
| depositTreasuryFeeSat | uint64 | Deposit treasury fee in satoshi (1e8 precision). This is an accurate value of the treasury fee that was actually cut upon minting. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | tbtcAmount Approximate amount of TBTC minted for the deposit. |

### _calculateDepositKey

```solidity
function _calculateDepositKey(bytes32 fundingTxHash, uint32 fundingOutputIndex) internal pure returns (uint256)
```

Calculates the deposit key for the given funding transaction
hash and funding output index.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| fundingTxHash | bytes32 | Funding transaction hash. |
| fundingOutputIndex | uint32 | Funding output index. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | depositKey Deposit key computed as `keccak256(fundingTxHash | reveal.fundingOutputIndex)`. This key can be used to refer to the deposit in the Bridge and TBTCVault contracts. |

### _calculateBitcoinTxHash

```solidity
function _calculateBitcoinTxHash(struct IBridgeTypes.BitcoinTxInfo txInfo) internal view returns (bytes32)
```

Calculates the Bitcoin transaction hash for the given Bitcoin
transaction data.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| txInfo | struct IBridgeTypes.BitcoinTxInfo | Bitcoin transaction data, see `IBridgeTypes.BitcoinTxInfo` struct. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bytes32 | txHash Bitcoin transaction hash. |

### _minDepositAmount

```solidity
function _minDepositAmount() internal view returns (uint256)
```

Returns minimum deposit amount.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Minimum deposit amount. In TBTC token decimals precision. |

