# Solidity API

## IWalletRegistry

### requestNewWallet

```solidity
function requestNewWallet() external
```

Requests a new wallet creation.

Only the Wallet Owner can call this function.

### closeWallet

```solidity
function closeWallet(bytes32 walletID) external
```

Closes an existing wallet.

Only the Wallet Owner can call this function.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletID | bytes32 | ID of the wallet. |

### seize

```solidity
function seize(uint96 amount, uint256 rewardMultiplier, address notifier, bytes32 walletID, uint32[] walletMembersIDs) external
```

Adds all signing group members of the wallet with the given ID
to the slashing queue of the staking contract. The notifier will
receive reward per each group member from the staking contract
notifiers treasury. The reward is scaled by the
`rewardMultiplier` provided as a parameter.

Only the Wallet Owner can call this function.
Requirements:
- The expression `keccak256(abi.encode(walletMembersIDs))` must
be exactly the same as the hash stored under `membersIdsHash`
for the given `walletID`. Those IDs are not directly stored
in the contract for gas efficiency purposes but they can be
read from appropriate `DkgResultSubmitted` and `DkgResultApproved`
events.
- `rewardMultiplier` must be between [0, 100].
- This function does revert if staking contract call reverts.
The calling code needs to handle the potential revert.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint96 | Amount of tokens to seize from each signing group member |
| rewardMultiplier | uint256 | Fraction of the staking contract notifiers reward the notifier should receive; should be between [0, 100] |
| notifier | address | Address of the misbehavior notifier |
| walletID | bytes32 | ID of the wallet |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members |

### getWalletPublicKey

```solidity
function getWalletPublicKey(bytes32 walletID) external view returns (bytes)
```

Gets public key of a wallet with a given wallet ID.
The public key is returned in an uncompressed format as a 64-byte
concatenation of X and Y coordinates.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletID | bytes32 | ID of the wallet. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bytes | Uncompressed public key of the wallet. |

### getWalletCreationState

```solidity
function getWalletCreationState() external view returns (enum EcdsaDkg.State)
```

Check current wallet creation state.

### isWalletMember

```solidity
function isWalletMember(bytes32 walletID, uint32[] walletMembersIDs, address operator, uint256 walletMemberIndex) external view returns (bool)
```

Checks whether the given operator is a member of the given
wallet signing group.

Requirements:
- The `operator` parameter must be an actual sortition pool operator.
- The expression `keccak256(abi.encode(walletMembersIDs))` must
be exactly the same as the hash stored under `membersIdsHash`
for the given `walletID`. Those IDs are not directly stored
in the contract for gas efficiency purposes but they can be
read from appropriate `DkgResultSubmitted` and `DkgResultApproved`
events.
- The `walletMemberIndex` must be in range [1, walletMembersIDs.length]

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| walletID | bytes32 | ID of the wallet |
| walletMembersIDs | uint32[] | Identifiers of the wallet signing group members |
| operator | address | Address of the checked operator |
| walletMemberIndex | uint256 | Position of the operator in the wallet signing group members list |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bool | True - if the operator is a member of the given wallet signing group. False - otherwise. |

