# Solidity API

## IL2WormholeGateway

Interface to the `L2WormholeGateway` contract.

### receiveTbtc

```solidity
function receiveTbtc(bytes vaa) external
```

See ./L2WormholeGateway.sol#receiveTbtc

## L2BitcoinDepositor

This contract is part of the direct bridging mechanism allowing
users to obtain ERC20 TBTC on supported L2 chains, without the need
to interact with the L1 tBTC ledger chain where minting occurs.

`L2BitcoinDepositor` is deployed on the L2 chain and interacts with
their L1 counterpart, the `L1BitcoinDepositor`, deployed on the
L1 tBTC ledger chain. Each `L1BitcoinDepositor` & `L2BitcoinDepositor`
pair is responsible for a specific L2 chain.

Please consult the `L1BitcoinDepositor` docstring for an
outline of the direct bridging mechanism

### wormholeRelayer

```solidity
contract IWormholeRelayer wormholeRelayer
```

`WormholeRelayer` contract on L2.

### l2WormholeGateway

```solidity
contract IL2WormholeGateway l2WormholeGateway
```

tBTC `L2WormholeGateway` contract on L2.

### l1ChainId

```solidity
uint16 l1ChainId
```

Wormhole chain ID of the corresponding L1 chain.

### l1BitcoinDepositor

```solidity
address l1BitcoinDepositor
```

tBTC `L1BitcoinDepositor` contract on the corresponding L1 chain.

### DepositInitialized

```solidity
event DepositInitialized(struct IBridgeTypes.BitcoinTxInfo fundingTx, struct IBridgeTypes.DepositRevealInfo reveal, address l2DepositOwner, address l2Sender)
```

### constructor

```solidity
constructor() public
```

### initialize

```solidity
function initialize(address _wormholeRelayer, address _l2WormholeGateway, uint16 _l1ChainId) external
```

### attachL1BitcoinDepositor

```solidity
function attachL1BitcoinDepositor(address _l1BitcoinDepositor) external
```

Sets the address of the `L1BitcoinDepositor` contract on the
corresponding L1 chain. This function solves the chicken-and-egg
problem of setting the `L1BitcoinDepositor` contract address
on the `L2BitcoinDepositor` contract and vice versa.

Requirements:
- Can be called only by the contract owner,
- The address must not be set yet,
- The new address must not be 0x0.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _l1BitcoinDepositor | address | Address of the `L1BitcoinDepositor` contract. |

### initializeDeposit

```solidity
function initializeDeposit(struct IBridgeTypes.BitcoinTxInfo fundingTx, struct IBridgeTypes.DepositRevealInfo reveal, address l2DepositOwner) external
```

Initializes the deposit process on L2 by emitting an event
containing the deposit data (funding transaction and
components of the P2(W)SH deposit address). The event is
supposed to be picked up by the relayer and used to initialize
the deposit on L1 through the `L1BitcoinDepositor` contract.

The alternative approach of using Wormhole Relayer to send the
deposit data to L1 was considered. However, it turned out to be
too expensive. For example, relying deposit data from Base L2 to
Ethereum L1 costs around ~0.045 ETH (~170 USD at the moment of writing).
Moreover, the next iteration of the direct bridging mechanism
assumes that no L2 transaction will be required to initialize the
deposit and the relayer should obtain the deposit data off-chain.
There is a high chance this function will be removed then.
That said, there was no sense to explore another cross-chain
messaging solutions. Relying on simple on-chain event and custom
off-chain relayer seems to be the most reasonable way to go. It
also aligns with the future direction of the direct bridging mechanism.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| fundingTx | struct IBridgeTypes.BitcoinTxInfo | Bitcoin funding transaction data. |
| reveal | struct IBridgeTypes.DepositRevealInfo | Deposit reveal data. |
| l2DepositOwner | address | Address of the L2 deposit owner. |

### receiveWormholeMessages

```solidity
function receiveWormholeMessages(bytes, bytes[] additionalVaas, bytes32 sourceAddress, uint16 sourceChain, bytes32) external payable
```

Receives Wormhole messages originating from the corresponding
`L1BitcoinDepositor` contract that lives on the L1 chain.
Messages are issued upon deposit finalization on L1 and
are supposed to carry the VAA of the Wormhole token transfer of
ERC20 L1 TBTC to the L2 chain. This contract performs some basic
checks and forwards the VAA to the `L2WormholeGateway` contract
that is authorized to withdraw the Wormhole-wrapped L2 TBTC
from the Wormhole Token Bridge (representing the ERC20 TBTC
locked on L1) and use it to mint the canonical L2 TBTC for the
deposit owner.

Requirements:
- Can be called only by the Wormhole Relayer contract,
- The source chain must be the expected L1 chain,
- The source address must be the corresponding
`L1BitcoinDepositor` contract,
- The message must carry exactly 1 additional VAA key representing
the token transfer.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
|  | bytes |  |
| additionalVaas | bytes[] | Additional VAAs that are part of the Wormhole message. |
| sourceAddress | bytes32 | Address of the source of the message (in Wormhole format). |
| sourceChain | uint16 | Wormhole chain ID of the source chain. |
|  | bytes32 |  |

