# WalletRegistryGovernance

## WalletRegistryGovernance

Owns the `WalletRegistry` contract and is responsible for updating its governable parameters in respect to the governance delay.

### newGovernanceDelay

```solidity
uint256 newGovernanceDelay
```

### governanceDelayChangeInitiated

```solidity
uint256 governanceDelayChangeInitiated
```

### newWalletRegistryGovernance

```solidity
address newWalletRegistryGovernance
```

### walletRegistryGovernanceTransferInitiated

```solidity
uint256 walletRegistryGovernanceTransferInitiated
```

### newWalletOwner

```solidity
address newWalletOwner
```

### walletOwnerChangeInitiated

```solidity
uint256 walletOwnerChangeInitiated
```

### newMinimumAuthorization

```solidity
uint96 newMinimumAuthorization
```

### minimumAuthorizationChangeInitiated

```solidity
uint256 minimumAuthorizationChangeInitiated
```

### newAuthorizationDecreaseDelay

```solidity
uint64 newAuthorizationDecreaseDelay
```

### authorizationDecreaseDelayChangeInitiated

```solidity
uint256 authorizationDecreaseDelayChangeInitiated
```

### newAuthorizationDecreaseChangePeriod

```solidity
uint64 newAuthorizationDecreaseChangePeriod
```

### authorizationDecreaseChangePeriodChangeInitiated

```solidity
uint256 authorizationDecreaseChangePeriodChangeInitiated
```

### newMaliciousDkgResultSlashingAmount

```solidity
uint96 newMaliciousDkgResultSlashingAmount
```

### maliciousDkgResultSlashingAmountChangeInitiated

```solidity
uint256 maliciousDkgResultSlashingAmountChangeInitiated
```

### newMaliciousDkgResultNotificationRewardMultiplier

```solidity
uint256 newMaliciousDkgResultNotificationRewardMultiplier
```

### maliciousDkgResultNotificationRewardMultiplierChangeInitiated

```solidity
uint256 maliciousDkgResultNotificationRewardMultiplierChangeInitiated
```

### newSortitionPoolRewardsBanDuration

```solidity
uint256 newSortitionPoolRewardsBanDuration
```

### sortitionPoolRewardsBanDurationChangeInitiated

```solidity
uint256 sortitionPoolRewardsBanDurationChangeInitiated
```

### newDkgSeedTimeout

```solidity
uint256 newDkgSeedTimeout
```

### dkgSeedTimeoutChangeInitiated

```solidity
uint256 dkgSeedTimeoutChangeInitiated
```

### newDkgResultChallengePeriodLength

```solidity
uint256 newDkgResultChallengePeriodLength
```

### dkgResultChallengePeriodLengthChangeInitiated

```solidity
uint256 dkgResultChallengePeriodLengthChangeInitiated
```

### newDkgResultChallengeExtraGas

```solidity
uint256 newDkgResultChallengeExtraGas
```

### dkgResultChallengeExtraGasChangeInitiated

```solidity
uint256 dkgResultChallengeExtraGasChangeInitiated
```

### newDkgResultSubmissionTimeout

```solidity
uint256 newDkgResultSubmissionTimeout
```

### dkgResultSubmissionTimeoutChangeInitiated

```solidity
uint256 dkgResultSubmissionTimeoutChangeInitiated
```

### newSubmitterPrecedencePeriodLength

```solidity
uint256 newSubmitterPrecedencePeriodLength
```

### dkgSubmitterPrecedencePeriodLengthChangeInitiated

```solidity
uint256 dkgSubmitterPrecedencePeriodLengthChangeInitiated
```

### newDkgResultSubmissionGas

```solidity
uint256 newDkgResultSubmissionGas
```

### dkgResultSubmissionGasChangeInitiated

```solidity
uint256 dkgResultSubmissionGasChangeInitiated
```

### newDkgResultApprovalGasOffset

```solidity
uint256 newDkgResultApprovalGasOffset
```

### dkgResultApprovalGasOffsetChangeInitiated

```solidity
uint256 dkgResultApprovalGasOffsetChangeInitiated
```

### newNotifyOperatorInactivityGasOffset

```solidity
uint256 newNotifyOperatorInactivityGasOffset
```

### notifyOperatorInactivityGasOffsetChangeInitiated

```solidity
uint256 notifyOperatorInactivityGasOffsetChangeInitiated
```

### newNotifySeedTimeoutGasOffset

```solidity
uint256 newNotifySeedTimeoutGasOffset
```

### notifySeedTimeoutGasOffsetChangeInitiated

```solidity
uint256 notifySeedTimeoutGasOffsetChangeInitiated
```

### newNotifyDkgTimeoutNegativeGasOffset

```solidity
uint256 newNotifyDkgTimeoutNegativeGasOffset
```

### notifyDkgTimeoutNegativeGasOffsetChangeInitiated

```solidity
uint256 notifyDkgTimeoutNegativeGasOffsetChangeInitiated
```

### newReimbursementPool

```solidity
address payable newReimbursementPool
```

### reimbursementPoolChangeInitiated

```solidity
uint256 reimbursementPoolChangeInitiated
```

### walletRegistry

```solidity
contract WalletRegistry walletRegistry
```

### governanceDelay

```solidity
uint256 governanceDelay
```

### GovernanceDelayUpdateStarted

```solidity
event GovernanceDelayUpdateStarted(uint256 governanceDelay, uint256 timestamp)
```

### GovernanceDelayUpdated

```solidity
event GovernanceDelayUpdated(uint256 governanceDelay)
```

### WalletRegistryGovernanceTransferStarted

```solidity
event WalletRegistryGovernanceTransferStarted(address newWalletRegistryGovernance, uint256 timestamp)
```

### WalletRegistryGovernanceTransferred

```solidity
event WalletRegistryGovernanceTransferred(address newWalletRegistryGovernance)
```

### WalletOwnerUpdateStarted

```solidity
event WalletOwnerUpdateStarted(address walletOwner, uint256 timestamp)
```

### WalletOwnerUpdated

```solidity
event WalletOwnerUpdated(address walletOwner)
```

### MinimumAuthorizationUpdateStarted

```solidity
event MinimumAuthorizationUpdateStarted(uint96 minimumAuthorization, uint256 timestamp)
```

### MinimumAuthorizationUpdated

```solidity
event MinimumAuthorizationUpdated(uint96 minimumAuthorization)
```

### AuthorizationDecreaseDelayUpdateStarted

```solidity
event AuthorizationDecreaseDelayUpdateStarted(uint64 authorizationDecreaseDelay, uint256 timestamp)
```

### AuthorizationDecreaseDelayUpdated

```solidity
event AuthorizationDecreaseDelayUpdated(uint64 authorizationDecreaseDelay)
```

### AuthorizationDecreaseChangePeriodUpdateStarted

```solidity
event AuthorizationDecreaseChangePeriodUpdateStarted(uint64 authorizationDecreaseChangePeriod, uint256 timestamp)
```

### AuthorizationDecreaseChangePeriodUpdated

```solidity
event AuthorizationDecreaseChangePeriodUpdated(uint64 authorizationDecreaseChangePeriod)
```

### MaliciousDkgResultSlashingAmountUpdateStarted

```solidity
event MaliciousDkgResultSlashingAmountUpdateStarted(uint256 maliciousDkgResultSlashingAmount, uint256 timestamp)
```

### MaliciousDkgResultSlashingAmountUpdated

```solidity
event MaliciousDkgResultSlashingAmountUpdated(uint256 maliciousDkgResultSlashingAmount)
```

### MaliciousDkgResultNotificationRewardMultiplierUpdateStarted

```solidity
event MaliciousDkgResultNotificationRewardMultiplierUpdateStarted(uint256 maliciousDkgResultNotificationRewardMultiplier, uint256 timestamp)
```

### MaliciousDkgResultNotificationRewardMultiplierUpdated

```solidity
event MaliciousDkgResultNotificationRewardMultiplierUpdated(uint256 maliciousDkgResultNotificationRewardMultiplier)
```

### SortitionPoolRewardsBanDurationUpdateStarted

```solidity
event SortitionPoolRewardsBanDurationUpdateStarted(uint256 sortitionPoolRewardsBanDuration, uint256 timestamp)
```

### SortitionPoolRewardsBanDurationUpdated

```solidity
event SortitionPoolRewardsBanDurationUpdated(uint256 sortitionPoolRewardsBanDuration)
```

### DkgSeedTimeoutUpdateStarted

```solidity
event DkgSeedTimeoutUpdateStarted(uint256 dkgSeedTimeout, uint256 timestamp)
```

### DkgSeedTimeoutUpdated

```solidity
event DkgSeedTimeoutUpdated(uint256 dkgSeedTimeout)
```

### DkgResultChallengePeriodLengthUpdateStarted

```solidity
event DkgResultChallengePeriodLengthUpdateStarted(uint256 dkgResultChallengePeriodLength, uint256 timestamp)
```

### DkgResultChallengePeriodLengthUpdated

```solidity
event DkgResultChallengePeriodLengthUpdated(uint256 dkgResultChallengePeriodLength)
```

### DkgResultChallengeExtraGasUpdateStarted

```solidity
event DkgResultChallengeExtraGasUpdateStarted(uint256 dkgResultChallengeExtraGas, uint256 timestamp)
```

### DkgResultChallengeExtraGasUpdated

```solidity
event DkgResultChallengeExtraGasUpdated(uint256 dkgResultChallengeExtraGas)
```

### DkgResultSubmissionTimeoutUpdateStarted

```solidity
event DkgResultSubmissionTimeoutUpdateStarted(uint256 dkgResultSubmissionTimeout, uint256 timestamp)
```

### DkgResultSubmissionTimeoutUpdated

```solidity
event DkgResultSubmissionTimeoutUpdated(uint256 dkgResultSubmissionTimeout)
```

### DkgSubmitterPrecedencePeriodLengthUpdateStarted

```solidity
event DkgSubmitterPrecedencePeriodLengthUpdateStarted(uint256 submitterPrecedencePeriodLength, uint256 timestamp)
```

### DkgSubmitterPrecedencePeriodLengthUpdated

```solidity
event DkgSubmitterPrecedencePeriodLengthUpdated(uint256 submitterPrecedencePeriodLength)
```

### DkgResultSubmissionGasUpdateStarted

```solidity
event DkgResultSubmissionGasUpdateStarted(uint256 dkgResultSubmissionGas, uint256 timestamp)
```

### DkgResultSubmissionGasUpdated

```solidity
event DkgResultSubmissionGasUpdated(uint256 dkgResultSubmissionGas)
```

### DkgResultApprovalGasOffsetUpdateStarted

```solidity
event DkgResultApprovalGasOffsetUpdateStarted(uint256 dkgResultApprovalGasOffset, uint256 timestamp)
```

### DkgResultApprovalGasOffsetUpdated

```solidity
event DkgResultApprovalGasOffsetUpdated(uint256 dkgResultApprovalGasOffset)
```

### NotifyOperatorInactivityGasOffsetUpdateStarted

```solidity
event NotifyOperatorInactivityGasOffsetUpdateStarted(uint256 notifyOperatorInactivityGasOffset, uint256 timestamp)
```

### NotifyOperatorInactivityGasOffsetUpdated

```solidity
event NotifyOperatorInactivityGasOffsetUpdated(uint256 notifyOperatorInactivityGasOffset)
```

### NotifySeedTimeoutGasOffsetUpdateStarted

```solidity
event NotifySeedTimeoutGasOffsetUpdateStarted(uint256 notifySeedTimeoutGasOffset, uint256 timestamp)
```

### NotifySeedTimeoutGasOffsetUpdated

```solidity
event NotifySeedTimeoutGasOffsetUpdated(uint256 notifySeedTimeoutGasOffset)
```

### NotifyDkgTimeoutNegativeGasOffsetUpdateStarted

```solidity
event NotifyDkgTimeoutNegativeGasOffsetUpdateStarted(uint256 notifyDkgTimeoutNegativeGasOffset, uint256 timestamp)
```

### NotifyDkgTimeoutNegativeGasOffsetUpdated

```solidity
event NotifyDkgTimeoutNegativeGasOffsetUpdated(uint256 notifyDkgTimeoutNegativeGasOffset)
```

### ReimbursementPoolUpdateStarted

```solidity
event ReimbursementPoolUpdateStarted(address reimbursementPool, uint256 timestamp)
```

### ReimbursementPoolUpdated

```solidity
event ReimbursementPoolUpdated(address reimbursementPool)
```

### onlyAfterGovernanceDelay

```solidity
modifier onlyAfterGovernanceDelay(uint256 changeInitiatedTimestamp)
```

Reverts if called before the governance delay elapses.

#### Parameters

| Name                     | Type    | Description                                       |
| ------------------------ | ------- | ------------------------------------------------- |
| changeInitiatedTimestamp | uint256 | Timestamp indicating the beginning of the change. |

### constructor

```solidity
constructor(contract WalletRegistry _walletRegistry, uint256 _governanceDelay) public
```

### upgradeRandomBeacon

```solidity
function upgradeRandomBeacon(address _newRandomBeacon) external
```

Upgrades the random beacon.

Can be called only by the contract owner.

#### Parameters

| Name              | Type    | Description               |
| ----------------- | ------- | ------------------------- |
| \_newRandomBeacon | address | New random beacon address |

### initializeWalletOwner

```solidity
function initializeWalletOwner(address _walletOwner) external
```

Initializes the Wallet Owner's address.

Can be called only by the contract owner. It can be called only if walletOwner has not been set before. It doesn't enforce a governance delay for the initial update. Any subsequent updates should be performed with beginWalletOwnerUpdate/finalizeWalletOwnerUpdate with respect of a governance delay.

#### Parameters

| Name          | Type    | Description                |
| ------------- | ------- | -------------------------- |
| \_walletOwner | address | The Wallet Owner's address |

### beginGovernanceDelayUpdate

```solidity
function beginGovernanceDelayUpdate(uint256 _newGovernanceDelay) external
```

Begins the governance delay update process.

Can be called only by the contract owner.

#### Parameters

| Name                 | Type    | Description          |
| -------------------- | ------- | -------------------- |
| \_newGovernanceDelay | uint256 | New governance delay |

### finalizeGovernanceDelayUpdate

```solidity
function finalizeGovernanceDelayUpdate() external
```

Finalizes the governance delay update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginWalletRegistryGovernanceTransfer

```solidity
function beginWalletRegistryGovernanceTransfer(address _newWalletRegistryGovernance) external
```

Begins the wallet registry governance transfer process.

Can be called only by the contract owner.

### finalizeWalletRegistryGovernanceTransfer

```solidity
function finalizeWalletRegistryGovernanceTransfer() external
```

Finalizes the wallet registry governance transfer process.

Can be called only by the contract owner, after the governance delay elapses.

### beginWalletOwnerUpdate

```solidity
function beginWalletOwnerUpdate(address _newWalletOwner) external
```

Begins the wallet owner update process.

Can be called only by the contract owner.

#### Parameters

| Name             | Type    | Description              |
| ---------------- | ------- | ------------------------ |
| \_newWalletOwner | address | New wallet owner address |

### finalizeWalletOwnerUpdate

```solidity
function finalizeWalletOwnerUpdate() external
```

Finalizes the wallet owner update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginMinimumAuthorizationUpdate

```solidity
function beginMinimumAuthorizationUpdate(uint96 _newMinimumAuthorization) external
```

Begins the minimum authorization amount update process.

Can be called only by the contract owner.

#### Parameters

| Name                      | Type   | Description                       |
| ------------------------- | ------ | --------------------------------- |
| \_newMinimumAuthorization | uint96 | New minimum authorization amount. |

### finalizeMinimumAuthorizationUpdate

```solidity
function finalizeMinimumAuthorizationUpdate() external
```

Finalizes the minimum authorization amount update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginAuthorizationDecreaseDelayUpdate

```solidity
function beginAuthorizationDecreaseDelayUpdate(uint64 _newAuthorizationDecreaseDelay) external
```

Begins the authorization decrease delay update process.

Can be called only by the contract owner.

#### Parameters

| Name                            | Type   | Description                      |
| ------------------------------- | ------ | -------------------------------- |
| \_newAuthorizationDecreaseDelay | uint64 | New authorization decrease delay |

### finalizeAuthorizationDecreaseDelayUpdate

```solidity
function finalizeAuthorizationDecreaseDelayUpdate() external
```

Finalizes the authorization decrease delay update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginAuthorizationDecreaseChangePeriodUpdate

```solidity
function beginAuthorizationDecreaseChangePeriodUpdate(uint64 _newAuthorizationDecreaseChangePeriod) external
```

Begins the authorization decrease change period update process.

Can be called only by the contract owner.

#### Parameters

| Name                                   | Type   | Description                              |
| -------------------------------------- | ------ | ---------------------------------------- |
| \_newAuthorizationDecreaseChangePeriod | uint64 | New authorization decrease change period |

### finalizeAuthorizationDecreaseChangePeriodUpdate

```solidity
function finalizeAuthorizationDecreaseChangePeriodUpdate() external
```

Finalizes the authorization decrease change period update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginMaliciousDkgResultSlashingAmountUpdate

```solidity
function beginMaliciousDkgResultSlashingAmountUpdate(uint96 _newMaliciousDkgResultSlashingAmount) external
```

Begins the malicious DKG result slashing amount update process.

Can be called only by the contract owner.

#### Parameters

| Name                                  | Type   | Description                              |
| ------------------------------------- | ------ | ---------------------------------------- |
| \_newMaliciousDkgResultSlashingAmount | uint96 | New malicious DKG result slashing amount |

### finalizeMaliciousDkgResultSlashingAmountUpdate

```solidity
function finalizeMaliciousDkgResultSlashingAmountUpdate() external
```

Finalizes the malicious DKG result slashing amount update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginMaliciousDkgResultNotificationRewardMultiplierUpdate

```solidity
function beginMaliciousDkgResultNotificationRewardMultiplierUpdate(uint256 _newMaliciousDkgResultNotificationRewardMultiplier) external
```

Begins the DKG malicious result notification reward multiplier update process.

Can be called only by the contract owner.

#### Parameters

| Name                                                | Type    | Description                                              |
| --------------------------------------------------- | ------- | -------------------------------------------------------- |
| \_newMaliciousDkgResultNotificationRewardMultiplier | uint256 | New DKG malicious result notification reward multiplier. |

### finalizeMaliciousDkgResultNotificationRewardMultiplierUpdate

```solidity
function finalizeMaliciousDkgResultNotificationRewardMultiplierUpdate() external
```

Finalizes the DKG malicious result notification reward multiplier update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginDkgResultSubmissionGasUpdate

```solidity
function beginDkgResultSubmissionGasUpdate(uint256 _newDkgResultSubmissionGas) external
```

Begins the dkg result submission gas update process.

Can be called only by the contract owner.

#### Parameters

| Name                        | Type    | Description                    |
| --------------------------- | ------- | ------------------------------ |
| \_newDkgResultSubmissionGas | uint256 | New DKG result submission gas. |

### finalizeDkgResultSubmissionGasUpdate

```solidity
function finalizeDkgResultSubmissionGasUpdate() external
```

Finalizes the dkg result submission gas update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginDkgResultApprovalGasOffsetUpdate

```solidity
function beginDkgResultApprovalGasOffsetUpdate(uint256 _newDkgResultApprovalGasOffset) external
```

Begins the dkg approval gas offset update process.

Can be called only by the contract owner.

#### Parameters

| Name                            | Type    | Description                  |
| ------------------------------- | ------- | ---------------------------- |
| \_newDkgResultApprovalGasOffset | uint256 | New DKG result approval gas. |

### finalizeDkgResultApprovalGasOffsetUpdate

```solidity
function finalizeDkgResultApprovalGasOffsetUpdate() external
```

Finalizes the dkg result approval gas offset update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginNotifyOperatorInactivityGasOffsetUpdate

```solidity
function beginNotifyOperatorInactivityGasOffsetUpdate(uint256 _newNotifyOperatorInactivityGasOffset) external
```

Begins the notify operator inactivity gas offset update process.

Can be called only by the contract owner.

#### Parameters

| Name                                   | Type    | Description                                     |
| -------------------------------------- | ------- | ----------------------------------------------- |
| \_newNotifyOperatorInactivityGasOffset | uint256 | New operator inactivity notification gas offset |

### finalizeNotifyOperatorInactivityGasOffsetUpdate

```solidity
function finalizeNotifyOperatorInactivityGasOffsetUpdate() external
```

Finalizes the notify operator inactivity gas offset update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginNotifySeedTimeoutGasOffsetUpdate

```solidity
function beginNotifySeedTimeoutGasOffsetUpdate(uint256 _newNotifySeedTimeoutGasOffset) external
```

Begins the notify seed for DKG delivery timeout gas offset update process.

Can be called only by the contract owner.

#### Parameters

| Name                            | Type    | Description                                               |
| ------------------------------- | ------- | --------------------------------------------------------- |
| \_newNotifySeedTimeoutGasOffset | uint256 | New seed for DKG delivery timeout notification gas offset |

### finalizeNotifySeedTimeoutGasOffsetUpdate

```solidity
function finalizeNotifySeedTimeoutGasOffsetUpdate() external
```

Finalizes the notify seed for DKG delivery timeout gas offset update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginNotifyDkgTimeoutNegativeGasOffsetUpdate

```solidity
function beginNotifyDkgTimeoutNegativeGasOffsetUpdate(uint256 _newNotifyDkgTimeoutNegativeGasOffset) external
```

Begins the notify DKG timeout negative gas offset update process.

Can be called only by the contract owner.

#### Parameters

| Name                                   | Type    | Description                                          |
| -------------------------------------- | ------- | ---------------------------------------------------- |
| \_newNotifyDkgTimeoutNegativeGasOffset | uint256 | New DKG timeout negative gas notification gas offset |

### finalizeNotifyDkgTimeoutNegativeGasOffsetUpdate

```solidity
function finalizeNotifyDkgTimeoutNegativeGasOffsetUpdate() external
```

Finalizes the notify DKG timeout negative gas offset update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginReimbursementPoolUpdate

```solidity
function beginReimbursementPoolUpdate(address payable _newReimbursementPool) external
```

Begins the reimbursement pool update process.

Can be called only by the contract owner.

#### Parameters

| Name                   | Type            | Description             |
| ---------------------- | --------------- | ----------------------- |
| \_newReimbursementPool | address payable | New reimbursement pool. |

### finalizeReimbursementPoolUpdate

```solidity
function finalizeReimbursementPoolUpdate() external
```

Finalizes the reimbursement pool update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginSortitionPoolRewardsBanDurationUpdate

```solidity
function beginSortitionPoolRewardsBanDurationUpdate(uint256 _newSortitionPoolRewardsBanDuration) external
```

Begins the sortition pool rewards ban duration update process.

Can be called only by the contract owner.

#### Parameters

| Name                                 | Type    | Description                              |
| ------------------------------------ | ------- | ---------------------------------------- |
| \_newSortitionPoolRewardsBanDuration | uint256 | New sortition pool rewards ban duration. |

### finalizeSortitionPoolRewardsBanDurationUpdate

```solidity
function finalizeSortitionPoolRewardsBanDurationUpdate() external
```

Finalizes the sortition pool rewards ban duration update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginDkgSeedTimeoutUpdate

```solidity
function beginDkgSeedTimeoutUpdate(uint256 _newDkgSeedTimeout) external
```

Begins the DKG seed timeout update process.

Can be called only by the contract owner.

#### Parameters

| Name                | Type    | Description                    |
| ------------------- | ------- | ------------------------------ |
| \_newDkgSeedTimeout | uint256 | New DKG seed timeout in blocks |

### finalizeDkgSeedTimeoutUpdate

```solidity
function finalizeDkgSeedTimeoutUpdate() external
```

Finalizes the DKG seed timeout update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginDkgResultChallengePeriodLengthUpdate

```solidity
function beginDkgResultChallengePeriodLengthUpdate(uint256 _newDkgResultChallengePeriodLength) external
```

Begins the DKG result challenge period length update process.

Can be called only by the contract owner.

#### Parameters

| Name                                | Type    | Description                                      |
| ----------------------------------- | ------- | ------------------------------------------------ |
| \_newDkgResultChallengePeriodLength | uint256 | New DKG result challenge period length in blocks |

### finalizeDkgResultChallengePeriodLengthUpdate

```solidity
function finalizeDkgResultChallengePeriodLengthUpdate() external
```

Finalizes the DKG result challenge period length update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginDkgResultChallengeExtraGasUpdate

```solidity
function beginDkgResultChallengeExtraGasUpdate(uint256 _newDkgResultChallengeExtraGas) external
```

Begins the DKG result challenge extra gas update process.

Can be called only by the contract owner.

#### Parameters

| Name                            | Type    | Description                        |
| ------------------------------- | ------- | ---------------------------------- |
| \_newDkgResultChallengeExtraGas | uint256 | New DKG result challenge extra gas |

### finalizeDkgResultChallengeExtraGasUpdate

```solidity
function finalizeDkgResultChallengeExtraGasUpdate() external
```

Finalizes the DKG result challenge extra gas update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginDkgResultSubmissionTimeoutUpdate

```solidity
function beginDkgResultSubmissionTimeoutUpdate(uint256 _newDkgResultSubmissionTimeout) external
```

Begins the DKG result submission timeout update process.

Can be called only by the contract owner.

#### Parameters

| Name                            | Type    | Description                                 |
| ------------------------------- | ------- | ------------------------------------------- |
| \_newDkgResultSubmissionTimeout | uint256 | New DKG result submission timeout in blocks |

### finalizeDkgResultSubmissionTimeoutUpdate

```solidity
function finalizeDkgResultSubmissionTimeoutUpdate() external
```

Finalizes the DKG result submission timeout update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginDkgSubmitterPrecedencePeriodLengthUpdate

```solidity
function beginDkgSubmitterPrecedencePeriodLengthUpdate(uint256 _newSubmitterPrecedencePeriodLength) external
```

Begins the DKG submitter precedence period length update process.

Can be called only by the contract owner.

#### Parameters

| Name                                 | Type    | Description                                          |
| ------------------------------------ | ------- | ---------------------------------------------------- |
| \_newSubmitterPrecedencePeriodLength | uint256 | New DKG submitter precedence period length in blocks |

### finalizeDkgSubmitterPrecedencePeriodLengthUpdate

```solidity
function finalizeDkgSubmitterPrecedencePeriodLengthUpdate() external
```

Finalizes the DKG submitter precedence period length update process.

Can be called only by the contract owner, after the governance delay elapses.

### withdrawIneligibleRewards

```solidity
function withdrawIneligibleRewards(address recipient) external
```

Withdraws rewards belonging to operators marked as ineligible for sortition pool rewards.

Can be called only by the contract owner.

#### Parameters

| Name      | Type    | Description                     |
| --------- | ------- | ------------------------------- |
| recipient | address | Recipient of withdrawn rewards. |

### getRemainingGovernanceDelayUpdateTime

```solidity
function getRemainingGovernanceDelayUpdateTime() external view returns (uint256)
```

Get the time remaining until the governance delay can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingWalletRegistryGovernanceTransferDelayTime

```solidity
function getRemainingWalletRegistryGovernanceTransferDelayTime() external view returns (uint256)
```

Get the time remaining until the wallet registry governance can be transferred.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingMimimumAuthorizationUpdateTime

```solidity
function getRemainingMimimumAuthorizationUpdateTime() external view returns (uint256)
```

Get the time remaining until the minimum authorization amount can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingAuthorizationDecreaseDelayUpdateTime

```solidity
function getRemainingAuthorizationDecreaseDelayUpdateTime() external view returns (uint256)
```

Get the time remaining until the authorization decrease delay can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingAuthorizationDecreaseChangePeriodUpdateTime

```solidity
function getRemainingAuthorizationDecreaseChangePeriodUpdateTime() external view returns (uint256)
```

Get the time remaining until the authorization decrease change period can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingMaliciousDkgResultSlashingAmountUpdateTime

```solidity
function getRemainingMaliciousDkgResultSlashingAmountUpdateTime() external view returns (uint256)
```

Get the time remaining until the malicious DKG result slashing amount can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingMaliciousDkgResultNotificationRewardMultiplierUpdateTime

```solidity
function getRemainingMaliciousDkgResultNotificationRewardMultiplierUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG malicious result notification reward multiplier duration can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingSortitionPoolRewardsBanDurationUpdateTime

```solidity
function getRemainingSortitionPoolRewardsBanDurationUpdateTime() external view returns (uint256)
```

Get the time remaining until the sortition pool rewards ban duration can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingDkgSeedTimeoutUpdateTime

```solidity
function getRemainingDkgSeedTimeoutUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG seed timeout can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingDkgResultChallengePeriodLengthUpdateTime

```solidity
function getRemainingDkgResultChallengePeriodLengthUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG result challenge period length can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingDkgResultChallengeExtraGasUpdateTime

```solidity
function getRemainingDkgResultChallengeExtraGasUpdateTime() external view returns (uint256)
```

### getRemainingDkgResultSubmissionTimeoutUpdateTime

```solidity
function getRemainingDkgResultSubmissionTimeoutUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG result submission timeout can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingWalletOwnerUpdateTime

```solidity
function getRemainingWalletOwnerUpdateTime() external view returns (uint256)
```

Get the time remaining until the wallet owner can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingDkgSubmitterPrecedencePeriodLengthUpdateTime

```solidity
function getRemainingDkgSubmitterPrecedencePeriodLengthUpdateTime() external view returns (uint256)
```

Get the time remaining until the wallet owner can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingDkgResultSubmissionGasUpdateTime

```solidity
function getRemainingDkgResultSubmissionGasUpdateTime() external view returns (uint256)
```

Get the time remaining until the dkg result submission gas can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingDkgResultApprovalGasOffsetUpdateTime

```solidity
function getRemainingDkgResultApprovalGasOffsetUpdateTime() external view returns (uint256)
```

Get the time remaining until the dkg result approval gas offset can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingNotifyOperatorInactivityGasOffsetUpdateTime

```solidity
function getRemainingNotifyOperatorInactivityGasOffsetUpdateTime() external view returns (uint256)
```

Get the time remaining until the operator inactivity gas offset can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingNotifySeedTimeoutGasOffsetUpdateTime

```solidity
function getRemainingNotifySeedTimeoutGasOffsetUpdateTime() external view returns (uint256)
```

Get the time remaining until the seed for DKG delivery timeout gas offset can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingNotifyDkgTimeoutNegativeGasOffsetUpdateTime

```solidity
function getRemainingNotifyDkgTimeoutNegativeGasOffsetUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG timeout negative gas offset can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingReimbursementPoolUpdateTime

```solidity
function getRemainingReimbursementPoolUpdateTime() external view returns (uint256)
```

Get the time remaining until reimbursement pool can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingChangeTime

```solidity
function getRemainingChangeTime(uint256 changeTimestamp) internal view returns (uint256)
```

Gets the time remaining until the governable parameter update can be committed.

#### Parameters

| Name            | Type    | Description                                       |
| --------------- | ------- | ------------------------------------------------- |
| changeTimestamp | uint256 | Timestamp indicating the beginning of the change. |

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |
