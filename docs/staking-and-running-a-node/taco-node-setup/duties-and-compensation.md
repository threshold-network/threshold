# Duties & Compensation

### Node **Operator Duties**&#x20;

Running a TACo node is not a passive activity, but the on-going responsibilities for stakers are very manageable. To supply a high quality service and sustain the utility of the Threshold Network, TACo node operators must:&#x20;

* Keep the server/machine running TACo online and accessible at all times; such that incoming decryption requests are verified and answered immediately, and there is no delay in reaching a threshold and supplying decryption material to qualifying data recipients.&#x20;
* Ensure their machine is running the latest version of TACo. New version releases will be shared on the Threshold Discord [#announcements](https://discord.com/channels/866378471868727316/870384195636199455) channel – make sure you have notifications enabled. Installing software upgrades is straightforward using [nucypher-ops](broken-reference).

### **Deauthorization Delay**&#x20;

Adopters of TACo require reassurance that the cohorts of nodes managing their users' data will remain intact for extended durations. Although cohort members can be securely replaced, while maintaining a persistent public key, it is preferable for economic reasons to minimize the number of node replacement rituals – particularly in the genesis era of the service. Hence, TACo service provision ideally involves a commitment to provide service for years, rather than months.&#x20;

{% hint style="warning" %}
The _deauthorization dela_y is the time one must wait between initiating a withdrawal from TACo service provision and being able to complete that withdrawal.\
\
From genesis, the deauthorization delay is set to **6 months** (183 days).&#x20;
{% endhint %}

Note that this delay is universal and independent of any other token lock-up or bonus mechanism.&#x20;

### **Fees**&#x20;

The first version of TACo will launch with fee-paying adopters. They will each pay a duration-based fee upfront, which gets them 6 months of access to the TACo API and a corresponding cohort of nodes to manage access to their users' data. These fees are paid in **DAI**.&#x20;

Fees will be collected and held by the Coordinator contract, which will be temporarily placed under the control of the Threshold Council.&#x20;

Following a set of competing proposals and votes, the DAO will choose a fee distribution mechanism, which will use the accumulated fees in a way that is most beneficial to the network and participating stakers. Here are some ideas to consider ahead of this collective decision:&#x20;

* Should fees be automatically converted to T, which would support the Threshold token price, enable compensation in a single token, and increase protocol-owned liquidity?
* Should stakers whose node is a member of more cohorts be compensated with commensurate fees, or should fees be distributed based purely on availability (i.e. stand-by nodes earn the same as nodes in active cohorts)?&#x20;

### **Rewards**&#x20;

TACo node operators will earn **3.75%** APY from launch. This yield is maintained, irrespective of the fluctuations to the staking rate, by the [Stable Yield](https://forum.threshold.network/t/tip-003-threshold-network-reward-mechanisms-proposal-i-stable-yield-for-non-institutional-staker-welfare/82) mechanism. \`

Please note that once TACo launches, stakers will not earn any yield if they only run a Proxy Re-Encryption node. PRE is being folded into the TACo application. &#x20;

### **Bonus**

To incentivize longer commitments at genesis, stakers can choose extended token lock-up periods and receive a corresponding bonus: \
\
**9 months** total (deauth delay + 3 month extension): **0.5%** extra yield\*&#x20;

**12 months** total (deauth delay + 6 months): **1%** extra yield\*&#x20;

**18 months** total (deauth delay + 12 months): **2%** extra yield\*&#x20;

**24 months** total (deauth delay + 18 months): **3%** extra yield\*

Note that the bonus is a _one-off_ incentive mechanism – stakers will commit to a single token lock-up duration prior to TACo's launch, and post-launch this commitment will **not be editable**. The opportunity to earn a bonus will also not be available post-launch. A more robust mechanism to incentivize longer commitments may be introduced in later versions – i.e. where stakers can freely and arbitrarily prolong their lock-up durations to earn extra yield.&#x20;

{% hint style="warning" %}
Post-launch, top-ups to one's stake will not count towards the bonus sum, **but will be subject to the same unlock horizon**. In other words, you cannot create multiple sub-stakes.&#x20;
{% endhint %}

Stakers may only start the clock on the deauthorization delay once the one-off bonus lock-up extension period has elapsed.&#x20;

The bonus will likely be withdrawable immediately – i.e. stakers will not have to wait for their stake to unlock.&#x20;

\*These bonus figures are provisional must be validated by the Threshold DAO prior to launch.&#x20;
