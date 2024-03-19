# Solidity API

## IBridgeTypes

Namespace which groups all types relevant to the IBridge interface.

This is a mirror of the real types used in the Bridge contract.
This way, the `integrator` subpackage does not need to import
anything from the `bridge` subpackage and explicitly depend on it.
This simplifies the dependency graph for integrators.

### BitcoinTxInfo

```solidity
struct BitcoinTxInfo {
  bytes4 version;
  bytes inputVector;
  bytes outputVector;
  bytes4 locktime;
}
```

### DepositRevealInfo

```solidity
struct DepositRevealInfo {
  uint32 fundingOutputIndex;
  bytes8 blindingFactor;
  bytes20 walletPubKeyHash;
  bytes20 refundPubKeyHash;
  bytes4 refundLocktime;
  address vault;
}
```

### DepositRequest

```solidity
struct DepositRequest {
  address depositor;
  uint64 amount;
  uint32 revealedAt;
  address vault;
  uint64 treasuryFee;
  uint32 sweptAt;
  bytes32 extraData;
}
```

## IBridge

Interface of the Bridge contract.

See bridge/Bridge.sol

### revealDepositWithExtraData

```solidity
function revealDepositWithExtraData(struct IBridgeTypes.BitcoinTxInfo fundingTx, struct IBridgeTypes.DepositRevealInfo reveal, bytes32 extraData) external
```

See {Bridge#revealDepositWithExtraData}

### deposits

```solidity
function deposits(uint256 depositKey) external view returns (struct IBridgeTypes.DepositRequest)
```

See {Bridge#deposits}

### depositParameters

```solidity
function depositParameters() external view returns (uint64 depositDustThreshold, uint64 depositTreasuryFeeDivisor, uint64 depositTxMaxFee, uint32 depositRevealAheadPeriod)
```

See {Bridge#depositParameters}

