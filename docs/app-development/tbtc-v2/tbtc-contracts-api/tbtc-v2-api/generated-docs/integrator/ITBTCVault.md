# Solidity API

## ITBTCVault

Interface of the TBTCVault contract.

See vault/TBTCVault.sol

### optimisticMintingRequests

```solidity
function optimisticMintingRequests(uint256 depositKey) external returns (uint64 requestedAt, uint64 finalizedAt)
```

See {TBTCVault#optimisticMintingRequests}

### optimisticMintingFeeDivisor

```solidity
function optimisticMintingFeeDivisor() external view returns (uint32)
```

See {TBTCVault#optimisticMintingFeeDivisor}

### tbtcToken

```solidity
function tbtcToken() external view returns (address)
```

See {TBTCVault#tbtcToken}

