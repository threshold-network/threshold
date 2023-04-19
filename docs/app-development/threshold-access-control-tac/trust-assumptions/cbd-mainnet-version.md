# CBD Mainnet Version

{% hint style="warning" %}
 Note that the CBD Mainnet version remains under development and is planned for release in Q2 2023. However, for developers considering integrating Conditions-Based Decryption into their application, it is worthwhile familiarizing oneself with the underlying trust model.
{% endhint %}

### Trust Packages

CBD allows adopting developers to pull a range of 'trust levers' in order to satisfy and assuage the trust, risk, cost, redundancy and latency preferences of their end-users. However, correctly selecting individual parameters across each and every trust/risk dimension requires a deep understanding of the underlying mechanisms and cryptology. Moreover, these parameters must be combined into a coherent bundle that, as a whole, aligns with the risk aversion and trust-minimization desiderata of end-users. Hence, to avoid burdening developers with onerous analysis, the Threshold team has constructed a set of pre-configured 'trust packages'. They are introduced and detailed [here](cbd-mainnet-version.md#trust-packages).&#x20;

The context for configuring the trust packages follows below.&#x20;

### Cohort-based trust assumptions&#x20;

The foundation of the Threshold Access Control trust model (and indeed, threshold cryptography in general) is the concept of a _Cohort;_ a group of Threshold nodes temporarily employed to either:

1. Collectively generate and manage part of a public key that can be used to encrypt one or more data payloads.&#x20;
2. Collectively manage a decryption fragment associated with a single data payload and provide the fragment to requesters who fulfill pre-specified conditions.

All Cohorts are parametrized on formation, including the Cohort _size_ (`n`) and Cohort _threshold_ (`m`). These parameters are the inputs for the following core trust assumptions: \
\
The first is the _orderly threshold_ assumption, wherein the protocol relies on a minimum number – the threshold – of node operators within each Cohort to follow the protocol correctly. For example, a 16-of-32 cohort would require at least **16** nodes to be online, responsive and (ideally) run an up-to-date version of CBD software. If any fewer than 16 are online, data requesters will be unable to retrieve decryption fragments. \
\
The second is the _honest threshold_ assumption, the protocol's most fundamental form of collusion-resistance – that is, protection against deliberate, unlawful attempts to access private data. In this case, the protocol relies on a minimum number of operators to be ‘honest’ – i.e. not susceptible to bribery, coercion or other attempts to maliciously collude. This minimum is calculated as the threshold node count (`m`) subtracted from the total cohort size, plus one (`n - m + 1`). Using the same example as before, a 16-of-32 Cohort would require a minimum of 32 - 16 + 1 = **17** honest operators. In other words, if at least 17 individual operators refuse to collude, there is nothing the remaining 15 operators can do, regardless of their war chest or aggregate stake power.&#x20;

Note that the _orderly threshold_ and _honest threshold_ assumptions are conceptually similar to the more common _honest majority_ assumption. However, they are more flexible than simply requiring those who control 50% of the staked tokens to be honest. Unlike most BFT or pBFT-based protocols, the _honest threshold_ can be partially decoupled from the operators’ stake weights, depending on the cohort sampling parameters specified by the developer or end-user.&#x20;

Some example cohort size and threshold configurations can be seen in [Create Security Optionality With Reusable Cohorts](../advanced-usage/create-security-optionality-with-reusable-cohorts.md)

### Sampling-based trust assumptions&#x20;

The _orderly threshold_ and _honest threshold_ trust assumptions above treat each Cohort as an isolated group, where the chosen parameters (`m-of-n`) determine the group’s redundancy, latency and collusion resistance. However, the reality is that each Cohort is selected from a larger sample of Threshold nodes, which is far larger than the typical/optimal size of each cohort – there is not necessarily much overlap between Cohorts.\
\
Therefore, the mechanisms through which nodes are selected to form Cohorts carry their own trust assumptions. More specifically, the _sampling parameters_ impact the security and collusion-resistance of a given data sharing flow. Sampling parametrization can be divided into; (1) those relating to frequency and prompting of (re-)sampling, and (2) compositional requirements to form a Cohort, besides the top-level `m` & `n` parameters.&#x20;

#### (1) Cohort refresh

Members of a Cohort can be replaced as frequently as is practical, with the main constraint being the bandwidth overhead of key discovery. A more regular re-sampling of Cohort members provides a form of increased collusion-resistance, as an attacker would have less time to discover, contact and bribe/coerce the requisite threshold of operators in order to access sensitive material.&#x20;

{% hint style="warning" %}
It could be argued that with very frequent Cohort refresh, a wily attacker could simply wait until a subset of operators they control happens to satisfy the threshold, however briefly that may be. However, this attack strategy would be of little value in most contexts, since it would be almost impossible to predict which encrypted message would coincide with the malicious subset-in-waiting. For example, an attacker seeking illicit access to market sensitive information (from a private DAO group chat) would be extraordinarily fortunate to temporarily control the requisite decryption fragments in the exact window that a market-moving message was shared.
{% endhint %}

The logic which _prompts_ the refreshing of Cohort members is also customizable. For example, a group chat application might include the option to mark a conversation as 'sensitive', in which case messages sent within this conversation will be assigned to and managed by fresh Cohorts with a greater frequency (or randomness) than regular messages.&#x20;

{% hint style="info" %}
It’s worth noting that the refreshing of Cohort members is unavoidable, regardless of the frequency or prompts chosen by a developer or end-user. This is because node operators will naturally wind down their operations and commitment to the network ('unbonding') over time. Although there are staker-facing protocol mechanisms in place to minimize the probability of a disorderly exodus, the proactive refreshing of Cohorts can also mitigate against abandonment of duties, for example by preemptively replacing operators with pending stake withdrawals (i.e. those who have initiated unbonding) with those with a longer-term economic commitment to the network (i.e. no unbonding initiated, or some longer token lock-up period).&#x20;
{% endhint %}

#### (2) Cohort composition beyond m & n

Developers may also toggle certain Cohort characteristics via the sampling mechanism. For example, they might force a percentage of Cohort positions to be filled exclusively by:

* Node operators who have staked a minimum (or maximum) sum of T tokens.
* Specific Ethereum addresses (e.g. nodes operated by the adopting developer themselves, or their partners).&#x20;
* Node operators who have not initiated stake unbonding in the past X months.&#x20;
* Node operators who also (or do not) provision service to other Threshold applications, such as tBTCv2.&#x20;
* Node operators who have a minimum historical availability (for example, as measured via their verifiable tBTCv2 activity).

### Population-based trust assumptions&#x20;

{% hint style="warning" %}
This section is under construction.
{% endhint %}

The protocol is also permissionless and psuedonymous (Ethereum addresses), which means the total _population_ of node operators will expand, contract and change its composition over time, and that change will not be perfectly discernible.&#x20;

The node population is also non-uniform; rather, it is comprised of a heterogeneous set of operators, ranging from the hobbyist to institutional stakers. Although there is a degree of standardization with respect to requisite machine memory, CPU power, latency and availability, the actual underlying servers are only observable anecdotally – i.e. this cannot yet be verified on-chain. However, this anecdotal, informal insight into the node population can be supplemented and corroborated with conclusions drawn from on-chain activity, such as slashing or unbonding events.

### Infrastructure-based trust assumptions&#x20;

{% hint style="warning" %}
This section is under construction.
{% endhint %}

