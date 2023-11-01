# Solidity API

## IRelay

Contains only the methods needed by tBTC v2. The Bitcoin relay
provides the difficulty of the previous and current epoch. One
difficulty epoch spans 2016 blocks.

### getCurrentEpochDifficulty

```solidity
function getCurrentEpochDifficulty() external view returns (uint256)
```

Returns the difficulty of the current epoch.

### getPrevEpochDifficulty

```solidity
function getPrevEpochDifficulty() external view returns (uint256)
```

Returns the difficulty of the previous epoch.

