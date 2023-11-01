# Solidity API

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

