# Solidity API

## SafeTUpgradeable

A wrapper around OpenZeppelin's `SafeERC20Upgradeable` but specific
to the T token. Use this library in upgradeable contracts. If your
contract is non-upgradeable, then the traditional `SafeERC20` works.
The motivation is to prevent upgradeable contracts that use T from
depending on the `Address` library, which can be problematic since
it uses `delegatecall`, which is discouraged by OpenZeppelin for use
in upgradeable contracts.

This implementation force-casts T to `IERC20Upgradeable` to make it
work with `SafeERC20Upgradeable`.

### safeTransfer

```solidity
function safeTransfer(contract T token, address to, uint256 value) internal
```

### safeTransferFrom

```solidity
function safeTransferFrom(contract T token, address from, address to, uint256 value) internal
```

