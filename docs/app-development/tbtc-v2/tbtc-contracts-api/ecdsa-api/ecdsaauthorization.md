# EcdsaAuthorization

## EcdsaAuthorization

Library managing the state of stake authorizations for ECDSA operator contract and the presence of operators in the sortition pool based on the stake authorized for them.

### Parameters

```solidity
struct Parameters {
  uint96 minimumAuthorization;
  uint64 authorizationDecreaseDelay;
  uint64 authorizationDecreaseChangePeriod;
}
```

### AuthorizationDecrease

```solidity
struct AuthorizationDecrease {
  uint96 decreasingBy;
  uint64 decreasingAt;
}
```

### Data

```solidity
struct Data {
  struct EcdsaAuthorization.Parameters parameters;
  mapping(address => address) stakingProviderToOperator;
  mapping(address => address) operatorToStakingProvider;
  mapping(address => struct EcdsaAuthorization.AuthorizationDecrease) pendingDecreases;
  uint256[46] __gap;
}
```

### OperatorRegistered

```solidity
event OperatorRegistered(address stakingProvider, address operator)
```

### AuthorizationIncreased

```solidity
event AuthorizationIncreased(address stakingProvider, address operator, uint96 fromAmount, uint96 toAmount)
```

### AuthorizationDecreaseRequested

```solidity
event AuthorizationDecreaseRequested(address stakingProvider, address operator, uint96 fromAmount, uint96 toAmount, uint64 decreasingAt)
```

### AuthorizationDecreaseApproved

```solidity
event AuthorizationDecreaseApproved(address stakingProvider)
```

### InvoluntaryAuthorizationDecreaseFailed

```solidity
event InvoluntaryAuthorizationDecreaseFailed(address stakingProvider, address operator, uint96 fromAmount, uint96 toAmount)
```

### OperatorJoinedSortitionPool

```solidity
event OperatorJoinedSortitionPool(address stakingProvider, address operator)
```

### OperatorStatusUpdated

```solidity
event OperatorStatusUpdated(address stakingProvider, address operator)
```

### setMinimumAuthorization

```solidity
function setMinimumAuthorization(struct EcdsaAuthorization.Data self, uint96 _minimumAuthorization) internal
```

Sets the minimum authorization for ECDSA application. Without at least the minimum authorization, staking provider is not eligible to join and operate in the network.

### setAuthorizationDecreaseDelay

```solidity
function setAuthorizationDecreaseDelay(struct EcdsaAuthorization.Data self, uint64 _authorizationDecreaseDelay) internal
```

Sets the authorization decrease delay. It is the time in seconds that needs to pass between the time authorization decrease is requested and the time the authorization decrease can be approved, no matter the authorization decrease amount.

### setAuthorizationDecreaseChangePeriod

```solidity
function setAuthorizationDecreaseChangePeriod(struct EcdsaAuthorization.Data self, uint64 _authorizationDecreaseChangePeriod) internal
```

Sets the authorization decrease change period. It is the time period before the authorization decrease delay end, during which the authorization decrease request can be overwritten.

### registerOperator

```solidity
function registerOperator(struct EcdsaAuthorization.Data self, address operator) internal
```

Used by staking provider to set operator address that will operate ECDSA node. The given staking provider can set operator address only one time. The operator address can not be changed and must be unique. Reverts if the operator is already set for the staking provider or if the operator address is already in use. Reverts if there is a pending authorization decrease for the staking provider.

### authorizationIncreased

```solidity
function authorizationIncreased(struct EcdsaAuthorization.Data self, address stakingProvider, uint96 fromAmount, uint96 toAmount) internal
```

Used by T staking contract to inform the application that the authorized stake amount for the given staking provider increased.

Reverts if the authorization amount is below the minimum.

The function is not updating the sortition pool. Sortition pool state needs to be updated by the operator with a call to `joinSortitionPool` or `updateOperatorStatus`.

Should only be callable by T staking contract.

### authorizationDecreaseRequested

```solidity
function authorizationDecreaseRequested(struct EcdsaAuthorization.Data self, address stakingProvider, uint96 fromAmount, uint96 toAmount) internal
```

Used by T staking contract to inform the application that the authorization decrease for the given staking provider has been requested.

Reverts if the amount after deauthorization would be non-zero and lower than the minimum authorization.

Reverts if another authorization decrease request is pending for the staking provider and not enough time passed since the original request (see `authorizationDecreaseChangePeriod`).

If the operator is not known (`registerOperator` was not called) it lets to `approveAuthorizationDecrease` immediately. If the operator is known (`registerOperator` was called), the operator needs to update state of the sortition pool with a call to `joinSortitionPool` or `updateOperatorStatus`. After the sortition pool state is in sync, authorization decrease delay starts.

After authorization decrease delay passes, authorization decrease request needs to be approved with a call to `approveAuthorizationDecrease` function.

If there is a pending authorization decrease request, it is overwritten, but only if enough time passed since the original request. Otherwise, the function reverts.

Should only be callable by T staking contract.

### approveAuthorizationDecrease

```solidity
function approveAuthorizationDecrease(struct EcdsaAuthorization.Data self, contract IStaking tokenStaking, address stakingProvider) internal
```

Approves the previously registered authorization decrease request. Reverts if authorization decrease delay have not passed yet or if the authorization decrease was not requested for the given staking provider.

### involuntaryAuthorizationDecrease

```solidity
function involuntaryAuthorizationDecrease(struct EcdsaAuthorization.Data self, contract IStaking tokenStaking, contract SortitionPool sortitionPool, address stakingProvider, uint96 fromAmount, uint96 toAmount) internal
```

Used by T staking contract to inform the application the authorization has been decreased for the given staking provider involuntarily, as a result of slashing.

If the operator is not known (`registerOperator` was not called) the function does nothing. The operator was never in a sortition pool so there is nothing to update.

If the operator is known, sortition pool is unlocked, and the operator is in the sortition pool, the sortition pool state is updated. If the sortition pool is locked, update needs to be postponed. Every other staker is incentivized to call `updateOperatorStatus` for the problematic operator to increase their own rewards in the pool.

Should only be callable by T staking contract.

### joinSortitionPool

```solidity
function joinSortitionPool(struct EcdsaAuthorization.Data self, contract IStaking tokenStaking, contract SortitionPool sortitionPool) internal
```

Lets the operator join the sortition pool. The operator address must be known - before calling this function, it has to be appointed by the staking provider by calling `registerOperator`. Also, the operator must have the minimum authorization required by ECDSA. Function reverts if there is no minimum stake authorized or if the operator is not known. If there was an authorization decrease requested, it is activated by starting the authorization decrease delay.

### updateOperatorStatus

```solidity
function updateOperatorStatus(struct EcdsaAuthorization.Data self, contract IStaking tokenStaking, contract SortitionPool sortitionPool, address operator) internal
```

Updates status of the operator in the sortition pool. If there was an authorization decrease requested, it is activated by starting the authorization decrease delay. Function reverts if the operator is not known.

### isOperatorUpToDate

```solidity
function isOperatorUpToDate(struct EcdsaAuthorization.Data self, contract IStaking tokenStaking, contract SortitionPool sortitionPool, address operator) internal view returns (bool)
```

Checks if the operator's authorized stake is in sync with operator's weight in the sortition pool. If the operator is not in the sortition pool and their authorized stake is non-zero, function returns false.

### eligibleStake

```solidity
function eligibleStake(struct EcdsaAuthorization.Data self, contract IStaking tokenStaking, address stakingProvider) internal view returns (uint96)
```

Returns the current value of the staking provider's eligible stake. Eligible stake is defined as the currently authorized stake minus the pending authorization decrease. Eligible stake is what is used for operator's weight in the pool. If the authorized stake minus the pending authorization decrease is below the minimum authorization, eligible stake is 0.

This function can be exposed to the public in contrast to the second variant accepting `decreasingBy` as a parameter.

### eligibleStake

```solidity
function eligibleStake(struct EcdsaAuthorization.Data self, contract IStaking tokenStaking, address stakingProvider, uint96 decreasingBy) internal view returns (uint96)
```

Returns the current value of the staking provider's eligible stake. Eligible stake is defined as the currently authorized stake minus the pending authorization decrease. Eligible stake is what is used for operator's weight in the pool. If the authorized stake minus the pending authorization decrease is below the minimum authorization, eligible stake is 0.

This function is not intended to be exposes to the public. `decreasingBy` must be fetched from `pendingDecreases` mapping and it is passed as a parameter to optimize gas usage of functions that call `eligibleStake` and need to use `AuthorizationDecrease` fetched from `pendingDecreases` for some additional logic.

### pendingAuthorizationDecrease

```solidity
function pendingAuthorizationDecrease(struct EcdsaAuthorization.Data self, address stakingProvider) internal view returns (uint96)
```

Returns the amount of stake that is pending authorization decrease for the given staking provider. If no authorization decrease has been requested, returns zero.

### remainingAuthorizationDecreaseDelay

```solidity
function remainingAuthorizationDecreaseDelay(struct EcdsaAuthorization.Data self, address stakingProvider) internal view returns (uint64)
```

Returns the remaining time in seconds that needs to pass before the requested authorization decrease can be approved. If the sortition pool state was not updated yet by the operator after requesting the authorization decrease, returns `type(uint64).max`.
