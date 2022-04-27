# 🗳 Governance Process

### Proposal Community Process

1. **Forum discussion**: A Threshold community member posts a potential proposal on the Threshold governance forums. Community members who post the initial proposal are encouraged to get active in Meta governance discussions within the community on forums and in Discord to earn support for the proposal. There is no minimum token threshold required to create a new proposal on the forums. Community mods reserve the right to moderate proposals during this stage by simply deleting the proposal from the forums.
2. **Temperature Check:** Once a potential proposal has enough traction and discussion within the community, it can proceed for a temperature check, which requires off-chain [snapshots](snapshots.md) to pass. If a proposal receives majority support during the temperature check snapshot, it is eligible to move onto the next step.&#x20;
3. **Proposal Creation**: A community member holding enough vote weight submits the proposal on-chain. The proposal enters a **two day vote delay period before voting** officially begins.
4. **Vote Period**: Proposal voting remains open for 10 days. If the proposal passes with enough quorum, it moves onto the next step; if the proposal fails, it is canceled. Creators and supporters of the proposal may bring a modified proposal forward again but it must be sufficiently different and pass requirements outlined in previous steps.
5. **Timelock Period**: Once a proposal is approved, the Governor smart contracts include an additional timelock delay of two days. Anyone can interact with the Governor smart contracts to queue an approved proposal into the Timelock contract.
6. **Execution**: After the timelock delay, anyone can execute an approved proposal.

###
