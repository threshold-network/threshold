# Solidity API

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

