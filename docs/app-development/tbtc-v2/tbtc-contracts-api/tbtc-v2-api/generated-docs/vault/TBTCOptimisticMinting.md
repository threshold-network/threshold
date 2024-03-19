# Solidity API

## TBTCOptimisticMinting

The Optimistic Minting mechanism allows to mint TBTC before
`TBTCVault` receives the Bank balance. There are two permissioned
sets in the system: Minters and Guardians, both set up in 1-of-n
mode. Minters observe the revealed deposits and request minting TBTC.
Any single Minter can perform this action. There is an
`optimisticMintingDelay` between the time of the request from
a Minter to the time TBTC is minted. During the time of the delay,
any Guardian can cancel the minting.

This functionality is a part of `TBTCVault`. It is implemented in
a separate abstract contract to achieve better separation of concerns
and easier-to-follow code.

### OptimisticMintingRequest

```solidity
struct OptimisticMintingRequest {
  uint64 requestedAt;
  uint64 finalizedAt;
}
```

### GOVERNANCE_DELAY

```solidity
uint256 GOVERNANCE_DELAY
```

The time delay that needs to pass between initializing and
finalizing the upgrade of governable parameters.

### SATOSHI_MULTIPLIER

```solidity
uint256 SATOSHI_MULTIPLIER
```

Multiplier to convert satoshi to TBTC token units.

### bridge

```solidity
contract Bridge bridge
```

### isOptimisticMintingPaused

```solidity
bool isOptimisticMintingPaused
```

Indicates if the optimistic minting has been paused. Only the
Governance can pause optimistic minting. Note that the pause of
the optimistic minting does not stop the standard minting flow
where wallets sweep deposits.

### optimisticMintingFeeDivisor

```solidity
uint32 optimisticMintingFeeDivisor
```

Divisor used to compute the treasury fee taken from each
optimistically minted deposit and transferred to the treasury
upon finalization of the optimistic mint. This fee is computed
as follows: `fee = amount / optimisticMintingFeeDivisor`.
For example, if the fee needs to be 2%, the
`optimisticMintingFeeDivisor` should be set to `50` because
`1/50 = 0.02 = 2%`.
The optimistic minting fee does not replace the deposit treasury
fee cut by the Bridge. The optimistic fee is a percentage AFTER
the treasury fee is cut:
`optimisticMintingFee = (depositAmount - treasuryFee) / optimisticMintingFeeDivisor`

### optimisticMintingDelay

```solidity
uint32 optimisticMintingDelay
```

The time that needs to pass between the moment the optimistic
minting is requested and the moment optimistic minting is
finalized with minting TBTC.

### isMinter

```solidity
mapping(address => bool) isMinter
```

Indicates if the given address is a Minter. Only Minters can
request optimistic minting.

### minters

```solidity
address[] minters
```

List of all Minters.

May be used to establish an order in which the Minters should
request for an optimistic minting.

### isGuardian

```solidity
mapping(address => bool) isGuardian
```

Indicates if the given address is a Guardian. Only Guardians can
cancel requested optimistic minting.

### optimisticMintingRequests

```solidity
mapping(uint256 => struct TBTCOptimisticMinting.OptimisticMintingRequest) optimisticMintingRequests
```

Collection of all revealed deposits for which the optimistic
minting was requested. Indexed by a deposit key computed as
`keccak256(fundingTxHash | fundingOutputIndex)`.

### optimisticMintingDebt

```solidity
mapping(address => uint256) optimisticMintingDebt
```

Optimistic minting debt value per depositor's address. The debt
represents the total value of all depositor's deposits revealed
to the Bridge that has not been yet swept and led to the
optimistic minting of TBTC. When `TBTCVault` sweeps a deposit,
the debt is fully or partially paid off, no matter if that
particular swept deposit was used for the optimistic minting or
not. The values are in 1e18 Ethereum precision.

### newOptimisticMintingFeeDivisor

```solidity
uint32 newOptimisticMintingFeeDivisor
```

New optimistic minting fee divisor value. Set only when the
parameter update process is pending. Once the update gets

### optimisticMintingFeeUpdateInitiatedTimestamp

```solidity
uint256 optimisticMintingFeeUpdateInitiatedTimestamp
```

The timestamp at which the update of the optimistic minting fee
divisor started. Zero if update is not in progress.

### newOptimisticMintingDelay

```solidity
uint32 newOptimisticMintingDelay
```

New optimistic minting delay value. Set only when the parameter
update process is pending. Once the update gets finalized, this

### optimisticMintingDelayUpdateInitiatedTimestamp

```solidity
uint256 optimisticMintingDelayUpdateInitiatedTimestamp
```

The timestamp at which the update of the optimistic minting
delay started. Zero if update is not in progress.

### OptimisticMintingRequested

```solidity
event OptimisticMintingRequested(address minter, uint256 depositKey, address depositor, uint256 amount, bytes32 fundingTxHash, uint32 fundingOutputIndex)
```

### OptimisticMintingFinalized

```solidity
event OptimisticMintingFinalized(address minter, uint256 depositKey, address depositor, uint256 optimisticMintingDebt)
```

### OptimisticMintingCancelled

```solidity
event OptimisticMintingCancelled(address guardian, uint256 depositKey)
```

### OptimisticMintingDebtRepaid

```solidity
event OptimisticMintingDebtRepaid(address depositor, uint256 optimisticMintingDebt)
```

### MinterAdded

```solidity
event MinterAdded(address minter)
```

### MinterRemoved

```solidity
event MinterRemoved(address minter)
```

### GuardianAdded

```solidity
event GuardianAdded(address guardian)
```

### GuardianRemoved

```solidity
event GuardianRemoved(address guardian)
```

### OptimisticMintingPaused

```solidity
event OptimisticMintingPaused()
```

### OptimisticMintingUnpaused

```solidity
event OptimisticMintingUnpaused()
```

### OptimisticMintingFeeUpdateStarted

```solidity
event OptimisticMintingFeeUpdateStarted(uint32 newOptimisticMintingFeeDivisor)
```

### OptimisticMintingFeeUpdated

```solidity
event OptimisticMintingFeeUpdated(uint32 newOptimisticMintingFeeDivisor)
```

### OptimisticMintingDelayUpdateStarted

```solidity
event OptimisticMintingDelayUpdateStarted(uint32 newOptimisticMintingDelay)
```

### OptimisticMintingDelayUpdated

```solidity
event OptimisticMintingDelayUpdated(uint32 newOptimisticMintingDelay)
```

### onlyMinter

```solidity
modifier onlyMinter()
```

### onlyGuardian

```solidity
modifier onlyGuardian()
```

### onlyOwnerOrGuardian

```solidity
modifier onlyOwnerOrGuardian()
```

### whenOptimisticMintingNotPaused

```solidity
modifier whenOptimisticMintingNotPaused()
```

### onlyAfterGovernanceDelay

```solidity
modifier onlyAfterGovernanceDelay(uint256 updateInitiatedTimestamp)
```

### constructor

```solidity
constructor(contract Bridge _bridge) internal
```

### _mint

```solidity
function _mint(address minter, uint256 amount) internal virtual
```

Mints the given amount of TBTC to the given depositor's address.
Implemented by TBTCVault.

### getMinters

```solidity
function getMinters() external view returns (address[])
```

Allows to fetch a list of all Minters.

### requestOptimisticMint

```solidity
function requestOptimisticMint(bytes32 fundingTxHash, uint32 fundingOutputIndex) external
```

Allows a Minter to request for an optimistic minting of TBTC.
The following conditions must be met:
- There is no optimistic minting request for the deposit,
finalized or not.
- The deposit with the given Bitcoin funding transaction hash
and output index has been revealed to the Bridge.
- The deposit has not been swept yet.
- The deposit is targeted into the TBTCVault.
- The optimistic minting is not paused.
After calling this function, the Minter has to wait for
`optimisticMintingDelay` before finalizing the mint with a call
to finalizeOptimisticMint.

The deposit done on the Bitcoin side must be revealed early enough
to the Bridge on Ethereum to pass the Bridge's validation. The
validation passes successfully only if the deposit reveal is done
respectively earlier than the moment when the deposit refund
locktime is reached, i.e. the deposit becomes refundable. It may
happen that the wallet does not sweep a revealed deposit and one of
the Minters requests an optimistic mint for that deposit just
before the locktime is reached. Guardians must cancel optimistic
minting for this deposit because the wallet will not be able to
sweep it. The on-chain optimistic minting code does not perform any
validation for gas efficiency: it would have to perform the same
validation as `validateDepositRefundLocktime` and expect the entire
`DepositRevealInfo` to be passed to assemble the expected script
hash on-chain. Guardians must validate if the deposit happened on
Bitcoin, that the script hash has the expected format, and that the
wallet is an active one so they can also validate the time left for
the refund.

### finalizeOptimisticMint

```solidity
function finalizeOptimisticMint(bytes32 fundingTxHash, uint32 fundingOutputIndex) external
```

Allows a Minter to finalize previously requested optimistic
minting. The following conditions must be met:
- The optimistic minting has been requested for the given
deposit.
- The deposit has not been swept yet.
- At least `optimisticMintingDelay` passed since the optimistic
minting was requested for the given deposit.
- The optimistic minting has not been finalized earlier for the
given deposit.
- The optimistic minting request for the given deposit has not
been canceled by a Guardian.
- The optimistic minting is not paused.
This function mints TBTC and increases `optimisticMintingDebt`
for the given depositor. The optimistic minting request is
marked as finalized.

### cancelOptimisticMint

```solidity
function cancelOptimisticMint(bytes32 fundingTxHash, uint32 fundingOutputIndex) external
```

Allows a Guardian to cancel optimistic minting request. The
following conditions must be met:
- The optimistic minting request for the given deposit exists.
- The optimistic minting request for the given deposit has not
been finalized yet.
Optimistic minting request is removed. It is possible to request
optimistic minting again for the same deposit later.

Guardians must validate the following conditions for every deposit
for which the optimistic minting was requested:
- The deposit happened on Bitcoin side and it has enough
confirmations.
- The optimistic minting has been requested early enough so that
the wallet has enough time to sweep the deposit.
- The wallet is an active one and it does perform sweeps or it will
perform sweeps once the sweeps are activated.

### addMinter

```solidity
function addMinter(address minter) external
```

Adds the address to the Minter list.

### removeMinter

```solidity
function removeMinter(address minter) external
```

Removes the address from the Minter list.

### addGuardian

```solidity
function addGuardian(address guardian) external
```

Adds the address to the Guardian set.

### removeGuardian

```solidity
function removeGuardian(address guardian) external
```

Removes the address from the Guardian set.

### pauseOptimisticMinting

```solidity
function pauseOptimisticMinting() external
```

Pauses the optimistic minting. Note that the pause of the
optimistic minting does not stop the standard minting flow
where wallets sweep deposits.

### unpauseOptimisticMinting

```solidity
function unpauseOptimisticMinting() external
```

Unpauses the optimistic minting.

### beginOptimisticMintingFeeUpdate

```solidity
function beginOptimisticMintingFeeUpdate(uint32 _newOptimisticMintingFeeDivisor) external
```

Begins the process of updating optimistic minting fee.
The fee is computed as follows:
`fee = amount / optimisticMintingFeeDivisor`.
For example, if the fee needs to be 2% of each deposit,
the `optimisticMintingFeeDivisor` should be set to `50` because
`1/50 = 0.02 = 2%`.

See the documentation for optimisticMintingFeeDivisor.

### finalizeOptimisticMintingFeeUpdate

```solidity
function finalizeOptimisticMintingFeeUpdate() external
```

Finalizes the update process of the optimistic minting fee.

### beginOptimisticMintingDelayUpdate

```solidity
function beginOptimisticMintingDelayUpdate(uint32 _newOptimisticMintingDelay) external
```

Begins the process of updating optimistic minting delay.

### finalizeOptimisticMintingDelayUpdate

```solidity
function finalizeOptimisticMintingDelayUpdate() external
```

Finalizes the update process of the optimistic minting delay.

### calculateDepositKey

```solidity
function calculateDepositKey(bytes32 fundingTxHash, uint32 fundingOutputIndex) public pure returns (uint256)
```

Calculates deposit key the same way as the Bridge contract.
The deposit key is computed as
`keccak256(fundingTxHash | fundingOutputIndex)`.

### repayOptimisticMintingDebt

```solidity
function repayOptimisticMintingDebt(address depositor, uint256 amount) internal returns (uint256)
```

Used by `TBTCVault.receiveBalanceIncrease` to repay the optimistic
minting debt before TBTC is minted. When optimistic minting is
finalized, debt equal to the value of the deposit being
a subject of the optimistic minting is incurred. When `TBTCVault`
sweeps a deposit, the debt is fully or partially paid off, no
matter if that particular deposit was used for the optimistic
minting or not.

See `TBTCVault.receiveBalanceIncrease`

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| depositor | address | The depositor whose balance increase is received. |
| amount | uint256 | The balance increase amount for the depositor received. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | The TBTC amount that should be minted after paying off the optimistic minting debt. |

