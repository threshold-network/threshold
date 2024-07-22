# 🗳️ Governance Process

### Lifecycle of a successful proposal

1. **Forum discussion**: A Threshold community member posts a potential proposal on the Threshold governance forums. Community members who post the initial proposal are encouraged to get active in Meta governance discussions within the community on forums and in Discord to earn support for the proposal. There is no minimum token threshold required to create a new proposal on the forums. Community mods reserve the right to moderate spam proposals during this stage by deleting the proposal from the forums.
2. **Temperature Check:** Once a potential proposal has enough traction and discussion within the community, it can proceed for a temperature check via an off-chain [snapshot](https://snapshot.org/#/threshold.eth). If a proposal receives majority support during the temperature check, it is eligible to move onto the next step.&#x20;
3. **Proposal Creation**: A community member holding enough vote weight (at least 0.25% of T total supply) submits the proposal on-chain. The proposal enters a **2-day delay period before voting** officially begins.
4. **Vote Period**: Proposal **voting remains open for 10 days**. If the proposal passes with enough quorum (at least 1.5% of T total supply), it moves onto the next step; if the proposal fails, it is canceled. Creators and supporters of the proposal may bring a modified proposal forward again but it must be sufficiently different and pass requirements outlined in previous steps.
5. **Timelock Period**: Once a proposal is approved, the Governor Bravo smart contracts include an additional **timelock delay of 2 days**. Anyone can interact with the Governor Bravo smart contracts to queue an approved proposal into the Timelock contract.
6. **Execution**: After the timelock delay, anyone can execute an approved proposal.

![The lifecycle of a successful proposal](<../../.gitbook/assets/Threshold DAO process.png>)

### Deviations in the proposal lifecycle

* **Council vetos:** During the on-chain phase, the Elected Council can veto any proposal. This is intended to be an extra security mechanism in the event that a dangerous proposal passes.&#x20;
* **Late quorum prevention:** The 10-days voting period is automatically extended when quorum is reached late, to prevent governance attacks that try to reach quorum at the last minute; in case a proposal reaches quorum less than 2 days before the deadline, the proposal deadline is **extended for 2 more days** from the moment the quorum was reached.
* **Off-chain proposals:** Some proposals do not imply any on-chain action (e.g. Council and Guild elections). In these cases, there is a Snapshot vote that takes 5 days for regular proposals and 7 days for elections, with no associated on-chain proposal.
