# Solidity API

## IVault

`IVault` is an interface for a smart contract consuming Bank
balances of other contracts or externally owned accounts (EOA).

### receiveBalanceIncrease

```solidity
function receiveBalanceIncrease(address[] depositors, uint256[] depositedAmounts) external
```

Called by the Bank in `increaseBalanceAndCall` function after
increasing the balance in the Bank for the vault. It happens in
the same transaction in which deposits were swept by the Bridge.
This allows the depositor to route their deposit revealed to the
Bridge to the particular smart contract (vault) in the same
transaction in which the deposit is revealed. This way, the
depositor does not have to execute additional transaction after
the deposit gets swept by the Bridge to approve and transfer
their balance to the vault.

The implementation must ensure this function can only be called
by the Bank. The Bank guarantees that the vault's balance was
increased by the sum of all deposited amounts before this function
is called, in the same transaction.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| depositors | address[] | Addresses of depositors whose deposits have been swept. |
| depositedAmounts | uint256[] | Amounts deposited by individual depositors and swept. |

