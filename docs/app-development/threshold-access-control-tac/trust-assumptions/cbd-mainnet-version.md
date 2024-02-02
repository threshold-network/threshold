# Mainnet Trust Model Foundation

For direct answers to trust burden-related questions, check out the [Provider Answers](mainnet-trust-disclosure-provider-answers.md) page for TACo. This page expands on those disclosures, explains the principles behind the overall trust model, considers the more subjective or domain-specific aspects of the model with respect to risk and security, and touches upon potential future features that would reduce the trust burden and provide more optionality for developers and end-users.

### Cohort-related trust assumptions&#x20;

The foundation of the TACo trust model – and much of threshold cryptography in general – is the concept of a _Cohort;_ a group of Threshold nodes temporarily employed to:

1. Collectively generate and manage part of a public key that can be used to encrypt one or more data payloads.&#x20;
2. Collectively manage a decryption fragment associated with a single data payload and provide the fragment to requesters who fulfill pre-specified conditions.

All Cohorts are parametrized on formation, including the Cohort _size_ (`n`) and Cohort _threshold_ (`m`). These parameters are the inputs for the following core trust assumptions:&#x20;

#### (1) Orderly Threshold&#x20;

The first is the _orderly threshold_ assumption, wherein the protocol relies on a minimum number – the threshold – of node operators within each Cohort to follow the protocol correctly. For example, a 16-of-32 cohort would require at least **16** nodes to be online, responsive, and run an up-to-date version of TACo software. If any fewer than 16 are online, data requesters will be unable to retrieve decryption fragments.&#x20;

#### (2) Honest Threshold&#x20;

The second is the _honest threshold_ assumption, the protocol's most fundamental form of collusion-resistance – that is, protection against deliberate, unlawful attempts to access private data. In this case, the protocol relies on a minimum number of nodes to be ‘honest’ – i.e. not susceptible to bribery, coercion,, or other attempts to maliciously collude. This minimum is calculated as the threshold node count (`m`) subtracted from the total cohort size, plus one (`n - m + 1`). Using the same example as before, a 16-of-32 Cohort would require a minimum of 32 - 16 + 1 = **17** honest nodes. In other words, if at least 17 individual operators refuse to collude, there is nothing the remaining 15 nodes can do, regardless of their war chest or aggregate stake power.&#x20;

Note that the _orderly threshold_ and _honest threshold_ assumptions are conceptually similar to the more common _honest majority_ assumption. However, they are more flexible than simply requiring those who control 67% of the staked tokens to be honest. Unlike most BFT or pBFT-based protocols, the _honest threshold_ can be partially decoupled from the nodes’ stake weights, depending on the cohort sampling parameters specified by the developer or end-user (see next section).&#x20;

### Nodes sampling-related trust assumptions&#x20;

The _orderly threshold_ and _honest threshold_ trust assumptions above treat each Cohort as an isolated group, where the chosen parameters (`m-of-n`) determine the group’s redundancy, latency, and collusion resistance. However, the reality is that each Cohort is selected from a larger sample of Threshold nodes, which is larger than the typical/optimal size of each cohort. \
\
Therefore, the mechanisms through which nodes are selected to form Cohorts carry their own trust assumptions. More specifically, the _sampling parameters_ impact the security and collusion-resistance of a given data-sharing flow. Sampling parametrization can be divided into; (1) those relating to frequency and prompting of (re-)sampling, and (2) compositional requirements to form a Cohort, besides the top-level `m` & `n` parameters.&#x20;

#### (1) Node replacement

Members of a Cohort of nodes are replaceable, with the main friction being the cost of gas to coordinate a DKG ritual, and to a lesser extent the bandwidth overhead of key discovery. Additionally, replaced TACo nodes do not automatically delete their decryption material, and therefore replacing nodes can theoretically expand the collusion surface. This problem is being addressed through R\&D into 'credible forgetability', and will require node clients to be more stateful. Therefore, early versions of TACo, node replacement will only occur in an emergency and sparingly as possible, as it is still too expensive and risky to be freely executed. \
\
In later versions, replacing and re-sampling Cohort members could provide increased collusion-resistance, as an attacker would have less time to discover, contact, and bribe or coerce the requisite threshold of nodes in order to access sensitive material.&#x20;

{% hint style="warning" %}
It could be argued that with very frequent Cohort refresh, a wily attacker could simply wait until a subset of nodes they control happens to satisfy the threshold. However, this attack strategy would be of little value in most contexts, since it would be almost impossible to predict which encrypted message would coincide with the malicious Cohort-subset-in-waiting. For example, an attacker seeking illicit access to market sensitive information, from a private DAO group chat, would need to temporarily control the requisite decryption fragments exactly within the brief time window that a market-moving message was shared.
{% endhint %}

The logic which _prompts_ the refreshing of Cohort members will be customizable in future versions. For example, a group chat application might include the option to mark a conversation as 'sensitive', in which case messages sent within this conversation will be assigned to and managed by nodes selected with a greater frequency (than regular messages), and/or selected with a higher standard of randomness (e.g. verifiable randomness via Random Beacon).&#x20;

{% hint style="info" %}
It’s worth noting that, over the long-term, the refreshing of Cohort members is unavoidable – regardless of the frequency or prompts chosen by a developer or end-user. This is because node operators will naturally wind down their operations and commitment to the network over time – this is known more broadly as 'unbonding', and in the context of Threshold is referred to as 'deauthorization'. Although there are staker-facing protocol mechanisms in place to minimize the probability of a disorderly exodus – including a minimum 6 month deauthorization delay – the proactive refreshing of Cohorts can also mitigate against abandonment of duties, for example by preemptively replacing nodes with pending stake withdrawals (i.e. those who have initiated deauthorization from TACo) with those with provable longer-term economic commitment to the network, including 9, 12, 18 and 24 month token lock-up extensions.&#x20;
{% endhint %}

#### (2) Cohort composition beyond `m` &  `n`

In future versions, adopting developers will also be able to toggle certain Cohort characteristics via the sampling mechanism. For example, they might force a percentage of Cohort positions to be filled exclusively by:

* Node operators who have staked a minimum (or maximum) sum of T tokens.
* Specific Ethereum addresses (e.g. nodes operated by the adopting developer themselves, or their partners).&#x20;
* Node operators who have not initiated deauthorization in the past X months.&#x20;
* Node operators who also (or do not) provision service to other Threshold applications, such as tBTCv2.&#x20;
* Node operators who have a minimum historical availability (for example, as measured via their verifiable tBTCv2 activity).

### Node population-related trust assumptions&#x20;

The protocol is permissionless and pseudonymous (using Ethereum addresses as unique identifiers). The total _population_ of node operators will expand, contract, and change its composition over time, and that change can be discerned only through public transactions.&#x20;

The node population is also non-uniform; rather, it is comprised of a heterogeneous set of node operators, ranging from the hobbyist to the institutional staker. Although there is a degree standardization with respect to requisite machine memory, CPU power, latency, and availability, this standardization is not formally enforced in early versions of TACo.&#x20;



