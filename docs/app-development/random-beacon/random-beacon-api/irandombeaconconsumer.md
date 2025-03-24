# IRandomBeaconConsumer

## IRandomBeaconConsumer

{% hint style="warning" %}
This file documents a contract which is not yet deployed to Mainnet.
{% endhint %}

### \_\_beaconCallback

```solidity
function __beaconCallback(uint256 relayEntry, uint256 blockNumber) external
```

Receives relay entry produced by Keep Random Beacon. This function should be called only by Keep Random Beacon.

#### Parameters

| Name        | Type    | Description                                                       |
| ----------- | ------- | ----------------------------------------------------------------- |
| relayEntry  | uint256 | Relay entry (random number) produced by Keep Random Beacon.       |
| blockNumber | uint256 | Block number at which the relay entry was submitted to the chain. |
