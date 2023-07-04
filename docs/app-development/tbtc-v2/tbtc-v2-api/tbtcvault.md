# Solidity API

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

