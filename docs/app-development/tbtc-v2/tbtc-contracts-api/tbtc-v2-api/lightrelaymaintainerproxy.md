# LightRelayMaintainerProxy

## LightRelayMaintainerProxy

The proxy contract that allows the relay maintainers to be refunded for the spent gas from the `ReimbursementPool`. When proving the next Bitcoin difficulty epoch, the maintainer calls the `LightRelayMaintainerProxy` which in turn calls the actual `LightRelay` contract.

### lightRelay

```solidity
contract ILightRelay lightRelay
```

### isAuthorized

```solidity
mapping(address => bool) isAuthorized
```

Stores the addresses that can maintain the relay. Those addresses are attested by the DAO.

The goal is to prevent a griefing attack by frontrunning relay maintainer which is responsible for retargetting the relay in the given round. The maintainer's transaction would revert with no gas refund. Having the ability to restrict maintainer addresses is also important in case the underlying relay contract has authorization requirements for callers.

### retargetGasOffset

```solidity
uint256 retargetGasOffset
```

Gas that is meant to balance the retarget overall cost. Can be

### LightRelayUpdated

```solidity
event LightRelayUpdated(address newRelay)
```

### MaintainerAuthorized

```solidity
event MaintainerAuthorized(address maintainer)
```

### MaintainerDeauthorized

```solidity
event MaintainerDeauthorized(address maintainer)
```

### RetargetGasOffsetUpdated

```solidity
event RetargetGasOffsetUpdated(uint256 retargetGasOffset)
```

### onlyRelayMaintainer

```solidity
modifier onlyRelayMaintainer()
```

### onlyReimbursableAdmin

```solidity
modifier onlyReimbursableAdmin()
```

### constructor

```solidity
constructor(contract ILightRelay _lightRelay, contract ReimbursementPool _reimbursementPool) public
```

### updateLightRelay

```solidity
function updateLightRelay(contract ILightRelay _lightRelay) external
```

Allows the governance to upgrade the `LightRelay` address.

The function does not implement any governance delay and does not check the status of the `LightRelay`. The Governance implementation needs to ensure all requirements for the upgrade are satisfied before executing this function.

### authorize

```solidity
function authorize(address maintainer) external
```

Authorizes the given address as a maintainer. Can only be called by the owner and the address of the maintainer must not be already authorized.

The function does not implement any governance delay.

#### Parameters

| Name       | Type    | Description                                     |
| ---------- | ------- | ----------------------------------------------- |
| maintainer | address | The address of the maintainer to be authorized. |

### deauthorize

```solidity
function deauthorize(address maintainer) external
```

Deauthorizes the given address as a maintainer. Can only be called by the owner and the address of the maintainer must be authorized.

The function does not implement any governance delay.

#### Parameters

| Name       | Type    | Description                                       |
| ---------- | ------- | ------------------------------------------------- |
| maintainer | address | The address of the maintainer to be deauthorized. |

### updateRetargetGasOffset

```solidity
function updateRetargetGasOffset(uint256 newRetargetGasOffset) external
```

Updates the values of retarget gas offset.

Can be called only by the contract owner. The caller is responsible for validating the parameter. The function does not implement any governance delay.

#### Parameters

| Name                 | Type    | Description              |
| -------------------- | ------- | ------------------------ |
| newRetargetGasOffset | uint256 | New retarget gas offset. |

### retarget

```solidity
function retarget(bytes headers) external
```

Wraps `LightRelay.retarget` call and reimburses the caller's transaction cost. Can only be called by an authorized relay maintainer.

See `LightRelay.retarget` function documentation.
