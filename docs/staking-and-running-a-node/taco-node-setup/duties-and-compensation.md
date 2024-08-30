# Duties, Compensation & Penalties

### Node **Operator Duties**&#x20;

{% hint style="danger" %}
Failure to adhere to node operator duties may result in on-chain punishments, including the withholding of rewards and slashing of stakes. See Violations & Penalties section below.&#x20;
{% endhint %}

Operating a TACo node requires active engagement. To provide high-quality service and maintain the reliability of the Threshold Network, TACo node operators must:

1. **Safeguard TACo Private Keys and Secret Mnemonic**: This includes creating an off-site record of the mnemonic assigned to your node in the initialization step and securing a copy of the keystore directory and passwords.
2. **Maintain Continuous Server Accessibility**: Ensure the server or machine running TACo software is online and accessible at all times, allowing for immediate verification and response to incoming decryption requests. This prevents delays in reaching a threshold and providing decryption material to qualifying data recipients.
3. **Update to the Latest TACo Version**: Ensure your node is running the latest version of TACo. New releases will be announced on the Threshold Discord #announcements channel; enable notifications to stay updated.

### Violations & Penalties \[WIP]

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
Since then, the fee model has been extended to include a **user-based** fee, based on the number of unique data producers (encryptors) an application requires at any given moment. For more detail on the dual fee model, see the latest payment structure and rules [here](../../app-development/staking-contract-and-dao/staking-contract-and-dao-api/vendingmachine.md).

All fees are paid in **DAI**.

Fees are collected and held by the Coordinator contract, which will be temporarily placed under the control of the Threshold Council. Following a set of competing proposals and votes, the DAO will choose a fee distribution mechanism, which will use the accumulated fees in a way that is most beneficial to the network and participating stakers. Proposals can be made by any TACo staker. \
\
Some questions to consider before making or voting on a proposal:&#x20;

* Should fees be automatically converted to T, which would support the Threshold token price, enable compensation in a single token, and increase protocol-owned liquidity?
* Should stakers whose node is a member of more cohorts be compensated with commensurate fees, or should fees be distributed based purely on availability. In other words, should stand-by nodes earn the same as nodes in active cohorts?

