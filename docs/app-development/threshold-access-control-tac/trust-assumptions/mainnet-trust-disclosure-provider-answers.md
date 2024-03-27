# Mainnet Trust Disclosure (Provider Answers)

This page serves as a proactive disclosure of the **TACo** application from a trust, risk and security perspective. It includes overviews of the Mainnet launch state (_Genesis_) and forthcoming states (_Future Versions_) of the service.&#x20;

### **Network-level distribution of trust**&#x20;

#### How many node operators (stakers) serve the network?&#x20;

_Genesis_\
Threshold nodes are primarily distinguishable by the staker's Ethereum address. As of February 2024, are [**43**](https://dune.com/embeds/3037106/5053569)[**9**](https://dune.com/embeds/3037106/5053569) distinct addresses that have activated a stake since the network's inception (in January 2022).&#x20;

TACo _cohorts_ – a group of nodes which provide the access control service to a single application or external project – have a minimum and default size of 30 members (each a node/server operated by a stakers). To reliably sample and generate distinct cohorts, TACo requires a total staker population least 60. \
\
As of February 2024, there are **95** nodes who have authorized stake to the TACo app and have spun up responsive and functioning TACo nodes.&#x20;

_Future Versions_\
Running any of the three major Threshold network applications is entirely permissionless. This means that the total node population expands and contracts over time, although the trend has been steady growth since network inception. Check out the following queries for the latest data:

* [Distinct addresses staking on Threshold since network inception](https://dune.com/embeds/3037106/5053569)
* [Total T Staked over time](https://dune.com/embeds/566587/1061539).&#x20;

#### How are node operators (stakers) incentivized to follow the protocol? How are they recruited, mobilized and coordinated?

_Genesis_\
Correct operator behavior is economically incentivized in the following ways:&#x20;

1. **Stable Yield** \
   When a staker commits to provide service on a given Threshold app – by authorizing their T denominated stake to that application – they are entitled to a subsidy via a consistent growth in that stake. This growth, expressed as yearly percentage, is enforced by the _Stable Yield Mechanism._ It serves as a consistent and predictable income that should cover a staker's outgoings, and therefore reduce the probability of volatility-driven insolvencies. For TACo, the stable yield is set to 3.75% APY, out of a total of 15% APY for service provision across all Threshold applications. Because this portion of yield reserved for TACo nodes is determined by the token-holder DAO, operators are collectively incentivized to provide a reliable service, lest this subsidy be reduced or removed as a consequence of malicious or negligent behavior.&#x20;
2. **Revenue**  \
   TACo charges its adopters an _availability-based fee –_ effectively a per-node, per-day rental fee denominated in DAI. These fees is collected and held the Threshold DAO. There will be a formal proposal and voting process to determine how fees will be distributed, but it is likely that the accumulated DAI will be converted into the T token before being split across active TACo stakers.&#x20;
3. **Token Holdings** \
   This is perhaps the most powerful incentive to follow the protocol correctly – because node operators' rewards, revenue and stake (collateral) are denominated in the T token, they are exposed to the perception of the network more than any other stakeholder. Although market forces have a large extraneous component, the collective behavior of stakers remains a key factor in the perceived utility and value of the applications hosted on Threshold. Moreover, the primary value of the T token is derived from the requirement to buy and stake T in order to earn revenue as a T node operator. Hence, it would be an act of economic self-harm to diverge from the protocol in any way, since it invariably reduces service quality, the utility of the Threshold network, the applications' current and potential revenue generation, by extension reduce the value of the T token, and hence every single operator's holdings and income.&#x20;

Stakers are recruited through the Threshold community, which primarily lives on the Threshold Discord server. There are also special events held like the Staker Townhall, which are an opportunity for both sides of the TACo market – adopters and stakers – to hear from one another. \
\
Stakers are mobilized to install the first or latest versions of the node client through announcements in the Threshold Discord server, and can check their node is active and running using the [Threshold Dashboard](https://dashboard.threshold.network/overview/network).

TACo software automates much of the coordination burden for stakers. The node-facing client connects to the network, joins cohorts, runs a DKG initialization (and refresh) rituals, holds decryption fragments, receives requests for decryption, checks condition fulfillment, and provisions decryption material – all without any intervention from the staker. However, they (and the public) are privy to all the [code](https://github.com/nucypher/nucypher) that performs these automations, and can check their client code for any unexpected behavior.&#x20;

_Future Versions_\
In later versions, TACo will charge an _action-based_ fee. As with the _availability-based_ fee, it will likely be converted into T before distribution, and further the incentive to follow the protocol and provide a reliable/secure service. More detail on this fee model can be found on the [Action-based fees](broken-reference) page.

### E**ntities with disproportionate power**

#### What damage can the DAO theoretically inflict on the network or its users?&#x20;

_Genesis_ \
Threshold's DAO is a token-holder DAO, meaning that anyone who holds the T token can participate in votes to upgrade the network. Not all updates involve a token-holder DAO vote, but changes to major economic parameters – such as altering the monetary policy of the network and minting more or less T tokens – require a simple majority and a quorum of 1.5% of all liquid + staked tokens to participate. Votes&#x20;

There are DAO attacks of various sophistication, but the general rule is that token-holder interests are aligned by a shared interest and exposure to the value of the T token. Given that all votes occur transparently, it's very hard to surreptitiously profit from a DAO attack that has to be orchestrated in public.&#x20;

With respect to TACo, there is no direct way for the DAO to compromise the end-to-end encryption protecting end-user data payloads. The most obvious attack would be to withhold staker compensation unless a threshold of stakers colluded to decrypt a target payload. However, this is likely to be ineffective, as executing the threat would require a public and drawn-out vote, which would almost certainly tank the value of the withheld funds. This would harm the attacker(s) and considerably blunt the threat.&#x20;

The more trustful constituent in the DAO is the Threshold Council, a multisig that is comprised of 9 elected individuals, voted in every 6 months. Although they cannot unilaterally make changes to economic parameters, a threshold (6-of-9) can veto proposals, and could theoretically hold the network hostage. This is a slightly more realistic scenario during genesis with respect to fees (as opposed to rewards), because TACo launches without a fee distribution mechanism in place, and the Council could repeatedly block the relevant update.&#x20;

In terms of the software itself, the Council also has control over several upgradeable application [contracts](https://github.com/nucypher/nucypher-contracts/blob/main/contracts/contracts/TACoApplication.sol) (e.g. Polygon child application). Although this power means the Council could push an update that prevent TACo from working, they cannot force adopting developers into downloading a client that unlawfully decrypts user data, and certainly not do so surreptitiously. \
\
_Future_\
The Threshold Council may be expanded, discontinued or have the scope of its powers reduced in order to minimize the attack surface via this weaker, more centralized component of Threshold Governance.&#x20;

### I**nfrastructural and software-related centralization**&#x20;

#### How does the user-facing client connect to the infrastructure provider’s network? How does it connect to other layers?

_Genesis_ \
The primary conduit to the Threshold network to access TACo services is via [_Porter_](https://docs.threshold.network/app-development/threshold-access-control-tac/porter)_:_&#x20;

* Porter is an intermediary gateway to the Threshold network. Adopters specify a Porter URI of their choice.&#x20;
* Porter cannot decrypt private data, as the data remains end-to-end encrypted through the Porter stage of an access request. However, Porter can theoretically block requests.&#x20;
* The NuCypher team runs a permanent Porter instance, but it is entirely [open source](https://github.com/nucypher/nucypher-porter). Threshold encourages TACo's adopting developers to spin up their own Porter, if that improves the trust burden – this depends on the unique trust assumptions of their use case(s).&#x20;

The default API is a web-oriented API written in Typescript ([taco-ts](https://github.com/nucypher/nucypher-ts)). There is also a reference Python API.&#x20;

* Code is primarily maintained and packaged by Threshold contributors, including in large part the NuCypher team.&#x20;
* All the code is open source and subject to formal and informal third-party oversight.&#x20;
* The user-facing library has various dependencies, both internal (e.g. [nucypher/nucypher](https://github.com/nucypher/nucypher), [nucypher/nucypher-core](https://github.com/nucypher/nucypher-core)) and external (e.g. [ethers](https://github.com/ethers-io/ethers.js)). There are also indirect cryptographic dependencies like [nucypher/ferveo](https://github.com/nucypher/ferveo).

_Future Versions_\
_C_ommunity-run Porters are currently being scoped out and developed. These will enable automated fall-backs if one Porter is non-available or deliberately blocking service. It is undecided as to whether Porter instances should become an extra component of running a TACo node, thus decentralizing it to the same extent.&#x20;

#### How does the infrastructure provider connect to the Ethereum network (or alternative base layer)?

_Genesis_ \
TACo is built as a multi-chain protocol, but at genesis supports conditions hosted on Ethereum & Polygon Mainnets.For connections to these networks, Threshold stakers are encouraged to run their own corresponding Ethereum and Polygon nodes. However, the majority make use of a convenient Web3 provider such as [Infura](https://www.infura.io/) or [Alchemy](https://www.alchemy.com/).&#x20;

The risk of Infura deliberately blocking requests from TACo nodes is very low. However, access gated by ERC-721 tokens (NFTs) is particularly dependent on Web3 providers, and vulnerable to \
intermediary modification of an Ethereum node's response. Some cohorts will contain a threshold of nodes that use the same provider, meaning that an orchestrated modified response would be accepted by the cohort.&#x20;

_Future Versions_\
Web3 provider diversity helps prevent the attack theorized above. It is quite straightforward, as an adopter or end-user, to select a Cohort of TACo nodes such no single Web3 provider has a plurality. If adopters request this functionality, this can be made available soon.&#x20;

#### How and from where is the client downloaded/installed? How open source is the project – both the user-facing client, and the node operator-facing client? How transparent is ongoing research, design & implementation? How clear-cut and coherent is the advertised path towards decentralization?

_Genesis_

* TACo can be built from the code directly or run from sources with signatures checked.&#x20;
* Both the [user-facing client](https://github.com/nucypher/nucypher-ts) and [node-facing client](https://github.com/nucypher/nucypher) are 100% open source.&#x20;
* External developers and adopters can contribute and submit pull requests freely.&#x20;
* Repository issues track the current system limitations and the corresponding mitigations in place. For example, in the [nucypher/nucypher](https://github.com/nucypher/nucypher/issues) and [nucypher/nucypher-ts](https://github.com/nucypher/nucypher-ts/issues) repos.
* Conversations among core contributors to TACo (voice & typed) are publicly available for the most part in the Discord [server](https://discord.com/channels/866378471868727316/870383642751430666).&#x20;
* TACo's [roadmap](https://github.com/orgs/nucypher/projects/31/views/2) (v7.0.0) is currently public, and roadmaps for subsequent versions will be made public once v.7.0.0 is released.&#x20;
* A detailed [trust model ](mainnet-trust-model-foundation.md)for Mainnet is also featured in the documentation.&#x20;
