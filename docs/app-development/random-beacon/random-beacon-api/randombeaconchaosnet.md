# RandomBeaconChaosnet

## RandomBeaconChaosnet

{% hint style="warning" %}
This file documents a contract which is not yet deployed to Mainnet.
{% endhint %}

A stub contract that will be used temporarily until the real-world random beacon client implementation is ready.

### authorizedRequesters

```solidity
mapping(address => bool) authorizedRequesters
```

Authorized addresses that can request a relay entry.

### entry

```solidity
uint256 entry
```

Arbitrary relay entry. Initially set to the Euler's number. It's updated after each relay entry request.

### RequesterAuthorizationUpdated

```solidity
event RequesterAuthorizationUpdated(address requester, bool isAuthorized)
```

### requestRelayEntry

```solidity
function requestRelayEntry(contract IRandomBeaconConsumer callbackContract) external
```

Executes the callback with an arbitrary relay entry number.

The caller must be an authorized requester.

#### Parameters

| Name             | Type                           | Description                                         |
| ---------------- | ------------------------------ | --------------------------------------------------- |
| callbackContract | contract IRandomBeaconConsumer | Beacon consumer callback contract - Wallet Registry |

### setRequesterAuthorization

```solidity
function setRequesterAuthorization(address requester, bool isAuthorized) external
```

Authorizes a requester of the relay entry.
