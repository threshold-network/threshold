# Solidity API

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

