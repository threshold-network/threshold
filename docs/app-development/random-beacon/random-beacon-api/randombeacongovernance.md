# RandomBeaconGovernance

## RandomBeaconGovernance

{% hint style="warning" %}
This file documents a contract which is not yet deployed to Mainnet.
{% endhint %}

Owns the `RandomBeacon` contract and is responsible for updating its governable parameters in respect to governance delay individual for each parameter.

### newGovernanceDelay

```solidity
uint256 newGovernanceDelay
```

### governanceDelayChangeInitiated

```solidity
uint256 governanceDelayChangeInitiated
```

### newRandomBeaconGovernance

```solidity
address newRandomBeaconGovernance
```

### randomBeaconGovernanceTransferInitiated

```solidity
uint256 randomBeaconGovernanceTransferInitiated
```

### newRelayEntrySoftTimeout

```solidity
uint256 newRelayEntrySoftTimeout
```

### relayEntrySoftTimeoutChangeInitiated

```solidity
uint256 relayEntrySoftTimeoutChangeInitiated
```

### newRelayEntryHardTimeout

```solidity
uint256 newRelayEntryHardTimeout
```

### relayEntryHardTimeoutChangeInitiated

```solidity
uint256 relayEntryHardTimeoutChangeInitiated
```

### newCallbackGasLimit

```solidity
uint256 newCallbackGasLimit
```

### callbackGasLimitChangeInitiated

```solidity
uint256 callbackGasLimitChangeInitiated
```

### newGroupCreationFrequency

```solidity
uint256 newGroupCreationFrequency
```

### groupCreationFrequencyChangeInitiated

```solidity
uint256 groupCreationFrequencyChangeInitiated
```

### newGroupLifetime

```solidity
uint256 newGroupLifetime
```

### groupLifetimeChangeInitiated

```solidity
uint256 groupLifetimeChangeInitiated
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

### newDkgSubmitterPrecedencePeriodLength

```solidity
uint256 newDkgSubmitterPrecedencePeriodLength
```

### dkgSubmitterPrecedencePeriodLengthChangeInitiated

```solidity
uint256 dkgSubmitterPrecedencePeriodLengthChangeInitiated
```

### newRelayEntrySubmissionFailureSlashingAmount

```solidity
uint96 newRelayEntrySubmissionFailureSlashingAmount
```

### relayEntrySubmissionFailureSlashingAmountChangeInitiated

```solidity
uint256 relayEntrySubmissionFailureSlashingAmountChangeInitiated
```

### newMaliciousDkgResultSlashingAmount

```solidity
uint96 newMaliciousDkgResultSlashingAmount
```

### maliciousDkgResultSlashingAmountChangeInitiated

```solidity
uint256 maliciousDkgResultSlashingAmountChangeInitiated
```

### newUnauthorizedSigningSlashingAmount

```solidity
uint96 newUnauthorizedSigningSlashingAmount
```

### unauthorizedSigningSlashingAmountChangeInitiated

```solidity
uint256 unauthorizedSigningSlashingAmountChangeInitiated
```

### newSortitionPoolRewardsBanDuration

```solidity
uint256 newSortitionPoolRewardsBanDuration
```

### sortitionPoolRewardsBanDurationChangeInitiated

```solidity
uint256 sortitionPoolRewardsBanDurationChangeInitiated
```

### newRelayEntryTimeoutNotificationRewardMultiplier

```solidity
uint256 newRelayEntryTimeoutNotificationRewardMultiplier
```

### relayEntryTimeoutNotificationRewardMultiplierChangeInitiated

```solidity
uint256 relayEntryTimeoutNotificationRewardMultiplierChangeInitiated
```

### newUnauthorizedSigningNotificationRewardMultiplier

```solidity
uint256 newUnauthorizedSigningNotificationRewardMultiplier
```

### unauthorizedSigningNotificationRewardMultiplierChangeInitiated

```solidity
uint256 unauthorizedSigningNotificationRewardMultiplierChangeInitiated
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

### newDkgMaliciousResultNotificationRewardMultiplier

```solidity
uint256 newDkgMaliciousResultNotificationRewardMultiplier
```

### dkgMaliciousResultNotificationRewardMultiplierChangeInitiated

```solidity
uint256 dkgMaliciousResultNotificationRewardMultiplierChangeInitiated
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

### newRelayEntrySubmissionGasOffset

```solidity
uint256 newRelayEntrySubmissionGasOffset
```

### relayEntrySubmissionGasOffsetChangeInitiated

```solidity
uint256 relayEntrySubmissionGasOffsetChangeInitiated
```

### randomBeacon

```solidity
contract RandomBeacon randomBeacon
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

### RandomBeaconGovernanceTransferStarted

```solidity
event RandomBeaconGovernanceTransferStarted(address newRandomBeaconGovernance, uint256 timestamp)
```

### RandomBeaconGovernanceTransferred

```solidity
event RandomBeaconGovernanceTransferred(address newRandomBeaconGovernance)
```

### RelayEntrySoftTimeoutUpdateStarted

```solidity
event RelayEntrySoftTimeoutUpdateStarted(uint256 relayEntrySoftTimeout, uint256 timestamp)
```

### RelayEntrySoftTimeoutUpdated

```solidity
event RelayEntrySoftTimeoutUpdated(uint256 relayEntrySoftTimeout)
```

### RelayEntryHardTimeoutUpdateStarted

```solidity
event RelayEntryHardTimeoutUpdateStarted(uint256 relayEntryHardTimeout, uint256 timestamp)
```

### RelayEntryHardTimeoutUpdated

```solidity
event RelayEntryHardTimeoutUpdated(uint256 relayEntryHardTimeout)
```

### CallbackGasLimitUpdateStarted

```solidity
event CallbackGasLimitUpdateStarted(uint256 callbackGasLimit, uint256 timestamp)
```

### CallbackGasLimitUpdated

```solidity
event CallbackGasLimitUpdated(uint256 callbackGasLimit)
```

### GroupCreationFrequencyUpdateStarted

```solidity
event GroupCreationFrequencyUpdateStarted(uint256 groupCreationFrequency, uint256 timestamp)
```

### GroupCreationFrequencyUpdated

```solidity
event GroupCreationFrequencyUpdated(uint256 groupCreationFrequency)
```

### GroupLifetimeUpdateStarted

```solidity
event GroupLifetimeUpdateStarted(uint256 groupLifetime, uint256 timestamp)
```

### GroupLifetimeUpdated

```solidity
event GroupLifetimeUpdated(uint256 groupLifetime)
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

### RelayEntrySubmissionFailureSlashingAmountUpdateStarted

```solidity
event RelayEntrySubmissionFailureSlashingAmountUpdateStarted(uint96 relayEntrySubmissionFailureSlashingAmount, uint256 timestamp)
```

### RelayEntrySubmissionFailureSlashingAmountUpdated

```solidity
event RelayEntrySubmissionFailureSlashingAmountUpdated(uint96 relayEntrySubmissionFailureSlashingAmount)
```

### MaliciousDkgResultSlashingAmountUpdateStarted

```solidity
event MaliciousDkgResultSlashingAmountUpdateStarted(uint96 maliciousDkgResultSlashingAmount, uint256 timestamp)
```

### MaliciousDkgResultSlashingAmountUpdated

```solidity
event MaliciousDkgResultSlashingAmountUpdated(uint96 maliciousDkgResultSlashingAmount)
```

### UnauthorizedSigningSlashingAmountUpdateStarted

```solidity
event UnauthorizedSigningSlashingAmountUpdateStarted(uint96 unauthorizedSigningSlashingAmount, uint256 timestamp)
```

### UnauthorizedSigningSlashingAmountUpdated

```solidity
event UnauthorizedSigningSlashingAmountUpdated(uint96 unauthorizedSigningSlashingAmount)
```

### SortitionPoolRewardsBanDurationUpdateStarted

```solidity
event SortitionPoolRewardsBanDurationUpdateStarted(uint256 sortitionPoolRewardsBanDuration, uint256 timestamp)
```

### SortitionPoolRewardsBanDurationUpdated

```solidity
event SortitionPoolRewardsBanDurationUpdated(uint256 sortitionPoolRewardsBanDuration)
```

### RelayEntryTimeoutNotificationRewardMultiplierUpdateStarted

```solidity
event RelayEntryTimeoutNotificationRewardMultiplierUpdateStarted(uint256 relayEntryTimeoutNotificationRewardMultiplier, uint256 timestamp)
```

### RelayEntryTimeoutNotificationRewardMultiplierUpdated

```solidity
event RelayEntryTimeoutNotificationRewardMultiplierUpdated(uint256 relayEntryTimeoutNotificationRewardMultiplier)
```

### UnauthorizedSigningNotificationRewardMultiplierUpdateStarted

```solidity
event UnauthorizedSigningNotificationRewardMultiplierUpdateStarted(uint256 unauthorizedSigningTimeoutNotificationRewardMultiplier, uint256 timestamp)
```

### UnauthorizedSigningNotificationRewardMultiplierUpdated

```solidity
event UnauthorizedSigningNotificationRewardMultiplierUpdated(uint256 unauthorizedSigningTimeoutNotificationRewardMultiplier)
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

### DkgMaliciousResultNotificationRewardMultiplierUpdateStarted

```solidity
event DkgMaliciousResultNotificationRewardMultiplierUpdateStarted(uint256 dkgMaliciousResultNotificationRewardMultiplier, uint256 timestamp)
```

### DkgMaliciousResultNotificationRewardMultiplierUpdated

```solidity
event DkgMaliciousResultNotificationRewardMultiplierUpdated(uint256 dkgMaliciousResultNotificationRewardMultiplier)
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

### RelayEntrySubmissionGasOffsetUpdateStarted

```solidity
event RelayEntrySubmissionGasOffsetUpdateStarted(uint256 relayEntrySubmissionGasOffset, uint256 timestamp)
```

### RelayEntrySubmissionGasOffsetUpdated

```solidity
event RelayEntrySubmissionGasOffsetUpdated(uint256 relayEntrySubmissionGasOffset)
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
constructor(contract RandomBeacon _randomBeacon, uint256 _governanceDelay) public
```

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

### beginRandomBeaconGovernanceTransfer

```solidity
function beginRandomBeaconGovernanceTransfer(address _newRandomBeaconGovernance) external
```

Begins the random beacon governance transfer process.

Can be called only by the current contract governance.

### finalizeRandomBeaconGovernanceTransfer

```solidity
function finalizeRandomBeaconGovernanceTransfer() external
```

Finalizes the random beacon governance transfer process.

Can be called only by the current contract governance, after the governance delay elapses.

### beginRelayEntrySoftTimeoutUpdate

```solidity
function beginRelayEntrySoftTimeoutUpdate(uint256 _newRelayEntrySoftTimeout) external
```

Begins the relay entry soft timeout update process.

Can be called only by the contract owner.

#### Parameters

| Name                       | Type    | Description                                  |
| -------------------------- | ------- | -------------------------------------------- |
| \_newRelayEntrySoftTimeout | uint256 | New relay entry submission timeout in blocks |

### finalizeRelayEntrySoftTimeoutUpdate

```solidity
function finalizeRelayEntrySoftTimeoutUpdate() external
```

Finalizes the relay entry soft timeout update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginRelayEntryHardTimeoutUpdate

```solidity
function beginRelayEntryHardTimeoutUpdate(uint256 _newRelayEntryHardTimeout) external
```

Begins the relay entry hard timeout update process.

Can be called only by the contract owner.

#### Parameters

| Name                       | Type    | Description                            |
| -------------------------- | ------- | -------------------------------------- |
| \_newRelayEntryHardTimeout | uint256 | New relay entry hard timeout in blocks |

### finalizeRelayEntryHardTimeoutUpdate

```solidity
function finalizeRelayEntryHardTimeoutUpdate() external
```

Finalizes the relay entry hard timeout update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginCallbackGasLimitUpdate

```solidity
function beginCallbackGasLimitUpdate(uint256 _newCallbackGasLimit) external
```

Begins the callback gas limit update process.

Can be called only by the contract owner.

#### Parameters

| Name                  | Type    | Description            |
| --------------------- | ------- | ---------------------- |
| \_newCallbackGasLimit | uint256 | New callback gas limit |

### finalizeCallbackGasLimitUpdate

```solidity
function finalizeCallbackGasLimitUpdate() external
```

Finalizes the callback gas limit update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginGroupCreationFrequencyUpdate

```solidity
function beginGroupCreationFrequencyUpdate(uint256 _newGroupCreationFrequency) external
```

Begins the group creation frequency update process.

Can be called only by the contract owner.

#### Parameters

| Name                        | Type    | Description                  |
| --------------------------- | ------- | ---------------------------- |
| \_newGroupCreationFrequency | uint256 | New group creation frequency |

### finalizeGroupCreationFrequencyUpdate

```solidity
function finalizeGroupCreationFrequencyUpdate() external
```

Finalizes the group creation frequency update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginGroupLifetimeUpdate

```solidity
function beginGroupLifetimeUpdate(uint256 _newGroupLifetime) external
```

Begins the group lifetime update process. Group lifetime needs to be shorter than the authorization decrease delay to ensure every active group is backed by enough stake. A new group lifetime value is in blocks and has to be calculated based on the average block time and authorization decrease delay which value is in seconds.

Can be called only by the contract owner.

#### Parameters

| Name               | Type    | Description                  |
| ------------------ | ------- | ---------------------------- |
| \_newGroupLifetime | uint256 | New group lifetime in blocks |

### finalizeGroupLifetimeUpdate

```solidity
function finalizeGroupLifetimeUpdate() external
```

Finalizes the group creation frequency update process.

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
function beginDkgSubmitterPrecedencePeriodLengthUpdate(uint256 _newDkgSubmitterPrecedencePeriodLength) external
```

Begins the DKG submitter precedence period length.

Can be called only by the contract owner.

#### Parameters

| Name                                    | Type    | Description                                          |
| --------------------------------------- | ------- | ---------------------------------------------------- |
| \_newDkgSubmitterPrecedencePeriodLength | uint256 | New DKG submitter precedence period length in blocks |

### finalizeDkgSubmitterPrecedencePeriodLengthUpdate

```solidity
function finalizeDkgSubmitterPrecedencePeriodLengthUpdate() external
```

Finalizes the DKG submitter precedence period length.

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

### beginRelayEntryTimeoutNotificationRewardMultiplierUpdate

```solidity
function beginRelayEntryTimeoutNotificationRewardMultiplierUpdate(uint256 _newRelayEntryTimeoutNotificationRewardMultiplier) external
```

Begins the relay entry timeout notification reward multiplier update process.

Can be called only by the contract owner.

#### Parameters

| Name                                               | Type    | Description                                             |
| -------------------------------------------------- | ------- | ------------------------------------------------------- |
| \_newRelayEntryTimeoutNotificationRewardMultiplier | uint256 | New relay entry timeout notification reward multiplier. |

### beginUnauthorizedSigningNotificationRewardMultiplierUpdate

```solidity
function beginUnauthorizedSigningNotificationRewardMultiplierUpdate(uint256 _newUnauthorizedSigningNotificationRewardMultiplier) external
```

Begins the unauthorized signing notification reward multiplier update process.

Can be called only by the contract owner.

#### Parameters

| Name                                                 | Type    | Description                                              |
| ---------------------------------------------------- | ------- | -------------------------------------------------------- |
| \_newUnauthorizedSigningNotificationRewardMultiplier | uint256 | New unauthorized signing notification reward multiplier. |

### finalizeUnauthorizedSigningNotificationRewardMultiplierUpdate

```solidity
function finalizeUnauthorizedSigningNotificationRewardMultiplierUpdate() external
```

Finalizes the unauthorized signing notification reward multiplier update process.

Can be called only by the contract owner, after the governance delay elapses.

### finalizeRelayEntryTimeoutNotificationRewardMultiplierUpdate

```solidity
function finalizeRelayEntryTimeoutNotificationRewardMultiplierUpdate() external
```

Finalizes the relay entry timeout notification reward multiplier update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginDkgMaliciousResultNotificationRewardMultiplierUpdate

```solidity
function beginDkgMaliciousResultNotificationRewardMultiplierUpdate(uint256 _newDkgMaliciousResultNotificationRewardMultiplier) external
```

Begins the DKG malicious result notification reward multiplier update process.

Can be called only by the contract owner.

#### Parameters

| Name                                                | Type    | Description                                              |
| --------------------------------------------------- | ------- | -------------------------------------------------------- |
| \_newDkgMaliciousResultNotificationRewardMultiplier | uint256 | New DKG malicious result notification reward multiplier. |

### finalizeDkgMaliciousResultNotificationRewardMultiplierUpdate

```solidity
function finalizeDkgMaliciousResultNotificationRewardMultiplierUpdate() external
```

Finalizes the DKG malicious result notification reward multiplier update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginRelayEntrySubmissionFailureSlashingAmountUpdate

```solidity
function beginRelayEntrySubmissionFailureSlashingAmountUpdate(uint96 _newRelayEntrySubmissionFailureSlashingAmount) external
```

Begins the relay entry submission failure slashing amount update process.

Can be called only by the contract owner.

#### Parameters

| Name                                           | Type   | Description                                        |
| ---------------------------------------------- | ------ | -------------------------------------------------- |
| \_newRelayEntrySubmissionFailureSlashingAmount | uint96 | New relay entry submission failure slashing amount |

### finalizeRelayEntrySubmissionFailureSlashingAmountUpdate

```solidity
function finalizeRelayEntrySubmissionFailureSlashingAmountUpdate() external
```

Finalizes the relay entry submission failure slashing amount update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginDkgResultSubmissionGasUpdate

```solidity
function beginDkgResultSubmissionGasUpdate(uint256 _newDkgResultSubmissionGas) external
```

Begins the DKG result submission gas update process.

Can be called only by the contract owner.

#### Parameters

| Name                        | Type    | Description                           |
| --------------------------- | ------- | ------------------------------------- |
| \_newDkgResultSubmissionGas | uint256 | New relay entry submission gas offset |

### finalizeDkgResultSubmissionGasUpdate

```solidity
function finalizeDkgResultSubmissionGasUpdate() external
```

Finalizes DKG result submission gas update process.

Can be called only by the contract owner, after the governance delay elapses.

### beginDkgResultApprovalGasOffsetUpdate

```solidity
function beginDkgResultApprovalGasOffsetUpdate(uint256 _newDkgResultApprovalGasOffset) external
```

Begins the DKG result approval gas offset update process.

Can be called only by the contract owner.

#### Parameters

| Name                            | Type    | Description                 |
| ------------------------------- | ------- | --------------------------- |
| \_newDkgResultApprovalGasOffset | uint256 | New DKG approval gas offset |

### finalizeDkgResultApprovalGasOffsetUpdate

```solidity
function finalizeDkgResultApprovalGasOffsetUpdate() external
```

Finalizes the DKG result approval gas offset update process.

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

### beginRelayEntrySubmissionGasOffsetUpdate

```solidity
function beginRelayEntrySubmissionGasOffsetUpdate(uint256 _newRelayEntrySubmissionGasOffset) external
```

Begins the relay entry submission gas offset update process.

Can be called only by the contract owner.

#### Parameters

| Name                               | Type    | Description                           |
| ---------------------------------- | ------- | ------------------------------------- |
| \_newRelayEntrySubmissionGasOffset | uint256 | New relay entry submission gas offset |

### finalizeRelayEntrySubmissionGasOffsetUpdate

```solidity
function finalizeRelayEntrySubmissionGasOffsetUpdate() external
```

Finalizes relay entry submission gas offset update process.

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

### beginUnauthorizedSigningSlashingAmountUpdate

```solidity
function beginUnauthorizedSigningSlashingAmountUpdate(uint96 _newUnauthorizedSigningSlashingAmount) external
```

Begins the unauthorized signing slashing amount update process.

Can be called only by the contract owner.

#### Parameters

| Name                                   | Type   | Description                              |
| -------------------------------------- | ------ | ---------------------------------------- |
| \_newUnauthorizedSigningSlashingAmount | uint96 | New unauthorized signing slashing amount |

### finalizeUnauthorizedSigningSlashingAmountUpdate

```solidity
function finalizeUnauthorizedSigningSlashingAmountUpdate() external
```

Finalizes the unauthorized signing slashing amount update process.

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

### setRequesterAuthorization

```solidity
function setRequesterAuthorization(address requester, bool isAuthorized) external
```

Set authorization for requesters that can request a relay entry. It can be done by the governance only.

#### Parameters

| Name         | Type    | Description                         |
| ------------ | ------- | ----------------------------------- |
| requester    | address | Requester, can be a contract or EOA |
| isAuthorized | bool    | True or false                       |

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

### getRemainingRandomBeaconGovernanceTransferDelayTime

```solidity
function getRemainingRandomBeaconGovernanceTransferDelayTime() external view returns (uint256)
```

Get the time remaining until the random beacon governance can be transferred.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingRelayEntrySoftTimeoutUpdateTime

```solidity
function getRemainingRelayEntrySoftTimeoutUpdateTime() external view returns (uint256)
```

Get the time remaining until the relay entry submission soft timeout can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingRelayEntryHardTimeoutUpdateTime

```solidity
function getRemainingRelayEntryHardTimeoutUpdateTime() external view returns (uint256)
```

Get the time remaining until the relay entry hard timeout can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingCallbackGasLimitUpdateTime

```solidity
function getRemainingCallbackGasLimitUpdateTime() external view returns (uint256)
```

Get the time remaining until the callback gas limit can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingGroupCreationFrequencyUpdateTime

```solidity
function getRemainingGroupCreationFrequencyUpdateTime() external view returns (uint256)
```

Get the time remaining until the group creation frequency can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingGroupLifetimeUpdateTime

```solidity
function getRemainingGroupLifetimeUpdateTime() external view returns (uint256)
```

Get the time remaining until the group lifetime can be updated.

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

Get the time remaining until the DKG result challenge extra gas can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingDkgResultSubmissionTimeoutUpdateTime

```solidity
function getRemainingDkgResultSubmissionTimeoutUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG result submission timeout can be updated.

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

### getRemainingRelayEntrySubmissionFailureSlashingAmountUpdateTime

```solidity
function getRemainingRelayEntrySubmissionFailureSlashingAmountUpdateTime() external view returns (uint256)
```

Get the time remaining until the relay entry submission failure slashing amount can be updated.

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

### getRemainingUnauthorizedSigningSlashingAmountUpdateTime

```solidity
function getRemainingUnauthorizedSigningSlashingAmountUpdateTime() external view returns (uint256)
```

Get the time remaining until the unauthorized signing slashing amount can be updated.

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

### getRemainingSortitionPoolRewardsBanDurationUpdateTime

```solidity
function getRemainingSortitionPoolRewardsBanDurationUpdateTime() external view returns (uint256)
```

Get the time remaining until the sortition pool rewards ban duration can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingRelayEntryTimeoutNotificationRewardMultiplierUpdateTime

```solidity
function getRemainingRelayEntryTimeoutNotificationRewardMultiplierUpdateTime() external view returns (uint256)
```

Get the time remaining until the relay entry timeout notification reward multiplier duration can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingUnauthorizedSigningNotificationRewardMultiplierUpdateTime

```solidity
function getRemainingUnauthorizedSigningNotificationRewardMultiplierUpdateTime() external view returns (uint256)
```

Get the time remaining until the unauthorized signing notification reward multiplier duration can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingDkgMaliciousResultNotificationRewardMultiplierUpdateTime

```solidity
function getRemainingDkgMaliciousResultNotificationRewardMultiplierUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG malicious result notification reward multiplier duration can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingDkgResultSubmissionGasUpdateTime

```solidity
function getRemainingDkgResultSubmissionGasUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG result submission gas duration can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingDkgResultApprovalGasOffsetUpdateTime

```solidity
function getRemainingDkgResultApprovalGasOffsetUpdateTime() external view returns (uint256)
```

Get the time remaining until the DKG approval gas offset duration can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingNotifyOperatorInactivityGasOffsetUpdateTime

```solidity
function getRemainingNotifyOperatorInactivityGasOffsetUpdateTime() external view returns (uint256)
```

Get the time remaining until the operator inactivity notification gas offset duration can be updated.

#### Return Values

| Name | Type    | Description                |
| ---- | ------- | -------------------------- |
| \[0] | uint256 | Remaining time in seconds. |

### getRemainingRelayEntrySubmissionGasOffsetUpdateTime

```solidity
function getRemainingRelayEntrySubmissionGasOffsetUpdateTime() external view returns (uint256)
```

Get the time remaining until the relay entry submission gas offset duration can be updated.

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
