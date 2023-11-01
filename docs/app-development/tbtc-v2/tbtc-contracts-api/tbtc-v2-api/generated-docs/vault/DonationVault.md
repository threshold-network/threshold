# Solidity API

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

