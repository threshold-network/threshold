# Solidity API

## IRandomBeacon

{% hint style="warning" %}
This file documents a code which is not yet deployed to Mainnet.
{% endhint %}

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

