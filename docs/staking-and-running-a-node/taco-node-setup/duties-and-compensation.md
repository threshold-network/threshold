# Duties, Compensation & Penalties

### Node **Operator Duties**&#x20;

{% hint style="danger" %}
Failure to adhere to node operator duties may result in on-chain punishments, including the withholding of rewards and slashing of stakes. See Violations & Penalties section below.&#x20;
{% endhint %}

Operating a TACo node requires active engagement. To provide high-quality service and maintain the reliability of the Threshold Network, TACo node operators must:

1. **Safeguard TACo private Keys and secret mnemonic**: This includes creating an off-site record of the mnemonic assigned to your node in the initialization step and securing a copy of the keystore directory and passwords.
2. **Maintain continuous server accessibility**: Ensure the server or machine running TACo software is online and accessible at all times, allowing for immediate verification and response to incoming decryption requests. This prevents delays in reaching a threshold and providing decryption material to qualifying data recipients.
3. **Update to the latest TACo version**: Ensure your node is running the latest version of TACo. New releases will be announced on the Threshold Discord #announcements channel; enable notifications to stay updated.

### Violations & Penalties

{% hint style="warning" %}
**As of September 2024, violations are being detected and logged on-chain via the Infraction Collector contract.**&#x20;

Today, avoiding committing attributable violations increases the likelihood that a node is selected by TACo's adopting developers to participate in DKG Initialization Rituals, and hence form part of TACo cohorts managing access to real-world data.

Reward withholding and stake slashing, as a consequence of committing attributable violations, are not yet in effect, but will be rolled out in the near future. &#x20;
{% endhint %}

Stakers operating TACo nodes must ensure that their nodes are up-to-date, available/reachable and correctly configured at all times. Failure to do may result in protocol divergent behavior, attributable failures, and the levying of economic penalties, including reward withholding and slashing of collateral.&#x20;

<img src="../../.gitbook/assets/file.excalidraw (2).svg" alt="High-level flow of TACo&#x27;s disincentive mechanism" class="gitbook-drawing">

Nodes are 'tested' during Distributed Key Generation (DKG) rituals, wherein the following violations are detectable, attributable and punishable on-chain:&#x20;

* Node is unreachable, non-responsive or offline.
* Node fails to submit a valid transcript or the transcript is missing.
* Node's keystore is mismatched with the ritual-coordinating smart contract. This implies the node operator has reset their node without following the correct recovery path, which is a protocol violation. If you have reset your node, please file a [support ticket](https://discord.com/channels/866378471868727316/1025113672185552938) – noting that resetting a TACo node (for any reason) is categorized as node mismanagement and may incur on-chain economic penalties in the future.&#x20;

The following DKG ritual failure modes are considered outside of the node operator's control and currently do not incur any penalties:&#x20;

* DKG ritual fails to generate a valid aggregation of transcripts.
* DKG ritual fails or times out due to blockchain or web3 provider issues.
* DKG ritual failure due to a bug in a node client release.

DKG rituals can occur at any time. There are two types of rituals:&#x20;

(1) **Initialization rituals.** These are DKG rituals initiated by TACo's adopting developers – the first step for a developer to integrate TACo access control in production and control their own cohort of nodes.&#x20;

(2) **Test & Heartbeat rituals.** These are manually initiated and automated dummy rituals, respectively. These generate statistics on the health of the network and flush out errant nodes ahead of Initialization rituals.&#x20;

DKG rituals are initiated without any warning – it is almost impossible to predict when a DKG ritual will occur, when your TACo node will be called into action, and when a violation could be detected and attributed. It is therefore safest to have your TACo node up-to-date, available/reachable, and correctly configured at all times.&#x20;

{% hint style="info" %}
All nodes must successfully complete several test/heartbeat rituals before being selected for an Initialization ritual with an adopting developer.&#x20;
{% endhint %}

The following parameters serve as a guide for economic penalties that will deployed in forthcoming versions of TACo. The first three violations committed by a node will be penalized via the withholding of monthly T rewards, with escalating severity for repeat offenses. If more than three violations occur, then the node operator will have a part of their stake slashed. \
\
**1st** violation: **30%** rewards withholding for 3 months.\
**2nd** violation: **60%** rewards withholding for 3 months. \
**3rd** violation: **90%** rewards withholding for 3 months.\
**4th** violation: (% TBD) slashing of stake, one-off.\
\
Note that the violation count is reset if a node manages to avoid committing any further violations during a given 3 month 'penalty period'. However, every violation restarts the penalty period. This means that, firstly, the duration over which reward withholding is levied is always three months from the date of that violation. Secondly, the violation count, and corresponding severity of punishment, will continue to ratchet up with each offense, until a node manages to avoid committing a violation for three months.&#x20;

{% hint style="info" %}
**Note for developers using TACo**\
Critical operations required for encryption & decryption follow a threshold design, hence isolated errant behavior will not disrupt end-user data sharing. However, DKG rituals, which are prerequisite for usage of TACO, are less fault tolerant and can fail if a single selected node fails to follow the protocol, inadvertently or otherwise. Hence, TACo's disincentive protocol is centered around DKG rituals, which (1) generate on-chain evidence for faults and (2) flush out negligent or bad actors before they can affect end-users.&#x20;
{% endhint %}

### **Deauthorization Delay**&#x20;

Adopters of TACo require reassurance that the cohorts of nodes managing their users' data will remain intact for extended durations. Although cohort members can be securely replaced, while maintaining a persistent public key, it is preferable for economic reasons to minimize the number of node replacement rituals – particularly in the genesis era of the service. Hence, TACo service provision ideally involves a commitment to provide service for years, rather than months.&#x20;

{% hint style="warning" %}
The _deauthorization dela_y is the time one must wait between initiating a withdrawal from TACo service provision and being able to complete that withdrawal.\
\
From genesis, the deauthorization delay is set to **6 months** (183 days).&#x20;
{% endhint %}

Note that this delay is universal and independent of any other token lock-up or bonus mechanism.&#x20;

### **Rewards**&#x20;

TACo node operators earn **3.75%** APY as soon as they authorize to the TACo application. This yield is maintained, irrespective of the fluctuations to the staking rate, by the [Stable Yield](https://forum.threshold.network/t/tip-003-threshold-network-reward-mechanisms-proposal-i-stable-yield-for-non-institutional-staker-welfare/82) mechanism. Hence, unless a staker is subject to a reward withholding penalty due to an attributable violation, they can expect to receive a consistent subsidy. &#x20;

### **Fees**&#x20;

The first version of TACo launched with fee-paying adopting developers. The first set of developers paid a **duration-based fee** upfront, giving them access to the TACo API and a corresponding cohort of nodes to manage access to their users' data. \
\
Since then, the fee model has been extended to include a **user-based** fee, based on the number of unique data producers (encryptors) an application requires at any given moment. For more detail on the dual fee model, see the latest payment structure and rules [here](https://github.com/nucypher/nucypher-contracts/issues/274#issuecomment-2322388661).

All fees are paid in Polygon **DAI**.

Fees are collected and held by the Coordinator contract, which will be temporarily placed under the control of the Threshold Council. Following a set of competing proposals and votes, the DAO will choose a fee distribution mechanism, which will use the accumulated fees in a way that is most beneficial to the network and participating stakers. Proposals can be made by any TACo staker. \
\
Some questions to consider before making or voting on a proposal:&#x20;

* Should fees be automatically converted to T, which would support the Threshold token price, enable compensation in a single token, and increase protocol-owned liquidity?
* Should stakers whose node is a member of more cohorts be compensated with commensurate fees, or should fees be distributed based purely on availability. In other words, should stand-by nodes earn the same as nodes in active cohorts?

