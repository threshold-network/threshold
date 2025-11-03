# L2WormholeGateway

## IWormholeTokenBridge

Wormhole Token Bridge interface. Contains only selected functions used by L2WormholeGateway.

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

Selected cross-ecosystem bridges are given the minting authority for tBTC token on L2 and sidechains. This contract gives a minting authority to the Wormhole Bridge.

The process of bridging from L1 to L2 (or sidechain) looks as follows:

1. There is a tBTC holder on L1. The holder goes to the Wormhole Portal and selects the chain they want to bridge to.
2. The holder submits one transaction to L1 locking their tBTC tokens in the bridge’s smart contract. After the transaction is mined, they wait about 15 minutes for the Ethereum block finality.
3. The holder submits one transaction to L2 that is minting tokens. After that transaction is mined, they have their tBTC on L2.

The process of bridging from L2 (or sidechain) to L1 looks as follows:

1. There is a tBTC holder on L2. That holder goes to the Wormhole Portal and selects one of the L2 chains they want to bridge from.
2. The holder submits one transaction to L2 that is burning the token. After the transaction is mined, they wait about 15 minutes for the L2 block finality.
3. The holder submits one transaction to L1 unlocking their tBTC tokens from the bridge’s smart contract. After that transaction is mined, they have their tBTC on L1.

This smart contract is integrated with step 3 of L1->L2 bridging and step 1 of L2->L1 or L2->L2 bridging. When the user redeems token on L2, this contract receives the Wormhole tBTC representation and mints the canonical tBTC in an equal amount. When user sends their token from L1, this contract burns the canonical tBTC and sends Wormhole tBTC representation through the bridge in an equal amount.

This contract is supposed to be deployed behind a transparent upgradeable proxy.

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

Maps Wormhole chain ID to the Wormhole tBTC gateway address on that chain. For example, this chain's ID should be mapped to this contract's address. If there is no Wormhole tBTC gateway address on the given chain, there is no entry in this mapping. The mapping holds addresses in a Wormhole-specific format, where Ethereum address is left-padded with zeros.

### mintingLimit

```solidity
uint256 mintingLimit
```

Minting limit for this gateway. Useful for early days of testing the system. The gateway can not mint more canonical tBTC than this limit.

### mintedAmount

```solidity
uint256 mintedAmount
```

The amount of tBTC minted by this contract. tBTC burned by this contract decreases this amount.

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

This function is called when the user sends their token from L2. The contract burns the canonical tBTC from the user and sends wormhole tBTC representation over the bridge. Keep in mind that when multiple bridges receive a minting authority on the canonical tBTC, this function may not be able to send all amounts of tBTC through the Wormhole bridge. The capability of Wormhole Bridge to send tBTC from the chain is limited to the amount of tBTC bridged through Wormhole to that chain.

Requirements:

* The sender must have at least `amount` of the canonical tBTC and it has to be approved for L2WormholeGateway.
* The L2WormholeGateway must have at least `amount` of the wormhole tBTC.
* The recipient must not be 0x0.
* The amount to transfer must not be 0,
* The amount to transfer must be >= 10^10 (1e18 precision). Depending if Wormhole tBTC gateway is registered on the target chain, this function uses transfer or transfer with payload over the Wormhole bridge.

#### Parameters

| Name           | Type    | Description                                                                            |
| -------------- | ------- | -------------------------------------------------------------------------------------- |
| amount         | uint256 | The amount of tBTC to be sent.                                                         |
| recipientChain | uint16  | The Wormhole recipient chain ID.                                                       |
| recipient      | bytes32 | The address of the recipient in the Wormhole format.                                   |
| arbiterFee     | uint256 | The Wormhole arbiter fee. Ignored if sending tBTC to chain with Wormhole tBTC gateway. |
| nonce          | uint32  | The Wormhole nonce used to batch messages together.                                    |

#### Return Values

| Name | Type   | Description                   |
| ---- | ------ | ----------------------------- |
| \[0] | uint64 | The Wormhole sequence number. |

### receiveTbtc

```solidity
function receiveTbtc(bytes encodedVm) external
```

This function is called when the user redeems their token on L2. The contract receives Wormhole tBTC representation and mints the canonical tBTC for the user. If the tBTC minting limit has been reached by this contract, instead of minting tBTC the receiver address receives Wormhole tBTC representation.

Requirements:

* The receiver of Wormhole tBTC should be the L2WormholeGateway contract.
* The receiver of the canonical tBTC should be abi-encoded in the payload.
* The receiver of the canonical tBTC must not be the zero address. The Wormhole Token Bridge contract has protection against redeeming the same VAA again. When a Token Bridge VAA is redeemed, its message body hash is stored in a map. This map is used to check whether the hash has already been set in this map. For this reason, this function does not have to be nonReentrant.

#### Parameters

| Name      | Type  | Description                                                     |
| --------- | ----- | --------------------------------------------------------------- |
| encodedVm | bytes | A byte array containing a Wormhole VAA signed by the guardians. |

### depositWormholeTbtc

```solidity
function depositWormholeTbtc(uint256 amount) external
```

Allows to deposit Wormhole tBTC token in exchange for canonical tBTC. Useful in a situation when user received wormhole tBTC instead of canonical tBTC. One example of such situation is when the minting limit was exceeded but the user minted anyway.

Requirements:

* The sender must have at least `amount` of the Wormhole tBTC and it has to be approved for L2WormholeGateway.
* The minting limit must allow for minting the given amount.

#### Parameters

| Name   | Type    | Description                             |
| ------ | ------- | --------------------------------------- |
| amount | uint256 | The amount of Wormhole tBTC to deposit. |

### updateGatewayAddress

```solidity
function updateGatewayAddress(uint16 chainId, bytes32 gateway) external
```

Lets the governance to update the tBTC gateway address on the chain with the given Wormhole ID.

Use toWormholeAddress function to convert between Ethereum and Wormhole address formats.

#### Parameters

| Name    | Type    | Description                                                      |
| ------- | ------- | ---------------------------------------------------------------- |
| chainId | uint16  | Wormhole ID of the chain.                                        |
| gateway | bytes32 | Address of tBTC gateway on the given chain in a Wormhole format. |

### updateMintingLimit

```solidity
function updateMintingLimit(uint256 _mintingLimit) external
```

Lets the governance to update the tBTC minting limit for this contract.

#### Parameters

| Name           | Type    | Description            |
| -------------- | ------- | ---------------------- |
| \_mintingLimit | uint256 | The new minting limit. |

### toWormholeAddress

```solidity
function toWormholeAddress(address _address) external pure returns (bytes32)
```

Converts Ethereum address into Wormhole format.

#### Parameters

| Name      | Type    | Description             |
| --------- | ------- | ----------------------- |
| \_address | address | The address to convert. |

### fromWormholeAddress

```solidity
function fromWormholeAddress(bytes32 _address) public pure returns (address)
```

Converts Wormhole address into Ethereum format.

#### Parameters

| Name      | Type    | Description             |
| --------- | ------- | ----------------------- |
| \_address | bytes32 | The address to convert. |

### normalize

```solidity
function normalize(uint256 amount) internal pure returns (uint256)
```

Eliminates the dust that cannot be bridged with Wormhole due to the decimal shift in the Wormhole Bridge contract. See https://github.com/wormhole-foundation/wormhole/blob/96682bdbeb7c87bfa110eade0554b3d8cbf788d2/ethereum/contracts/bridge/Bridge.sol#L276-L288
