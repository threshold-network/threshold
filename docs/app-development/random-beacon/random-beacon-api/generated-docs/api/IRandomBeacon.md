# Solidity API

## IRandomBeacon

### requestRelayEntry

```solidity
function requestRelayEntry(contract IRandomBeaconConsumer callbackContract) external
```

Creates a request to generate a new relay entry. Requires a
request fee denominated in T token.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| callbackContract | contract IRandomBeaconConsumer | Beacon consumer callback contract. |

