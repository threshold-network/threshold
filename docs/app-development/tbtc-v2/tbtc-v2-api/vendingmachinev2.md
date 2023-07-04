# Solidity API

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

