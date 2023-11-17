# tBTC Stakers FAQ

### Who are stakers? &#x20;

Threshold Network runs on staker nodes, where there is one Staker for one node. Stakers can operate their own node or stake via [Staking Providers](running-a-node/staking-providers.md) (also known as delegating). Nodes are virtual or physical computers that run cryptography software ([Keep Core Client)](https://github.com/keep-network/keep-core) to coordinate the decentralized operation.&#x20;

### How are stakers incentivized?

Stakers stake T tokens and earn staking rewards as part of their participation in the network. Rewards originate from the inflationary design of T token. Current reward emission rate is 15% annually if authorizing all Threshold applications.

### What is the minimum stake?

The minimum stake to run a node is 40,000T. Stakers must decide which applications they would like to authorize their stake to support. A stake can be partially or fully applied to any application, but cannot be below the minimum.

### How does Authorizing applications and reward allocation work?

As of Nov 14, 2023 there are two Threshold applications that can be staked: tBTC and PRE. tBTC has another application bundled with it, called Random Beacon, which must also be authorized in the stakers [dashboard](https://dashboard.threshold.network/).&#x20;

As referenced above, current reward emission rate is 15%. Rewards are allocated 75% to tBTC/Random Beacon and 25% to PRE/TACo. All applications must be fully authorized to earn the full 15% reward rate.

Rewards can be **estimated** as follows:

`monthly rewards = stake size * annual reward rate / 12 months * app %`

### How are rewards distributed?

Rewards become available within the first few days of the month for the prior month and must be claimed manually through the staking dashboard at [https://dashboard.threshold.network/](https://dashboard.threshold.network/).

### What are the costs of staking?&#x20;

Stakers who operate nodes incur infrastructure and operational costs. Infrastructure costs include running computers either physically or in the cloud. Operational costs include maintenance, upgrades, and troubleshooting. Staking Providers incur customer service costs in addition to the above.&#x20;

Stakers who delegate simply pay service fees to Staking Providers. Each Staking Provider has a different business model, and typically charge a maintenance fee together with a commission from staking rewards.&#x20;

### How many staker nodes are there?

As of Nov 14 2023, there are 149 tBTC nodes in Threshold Network. The number of nodes may change at any time. For most current figure, please visit [https://status.threshold.network/](https://status.threshold.network/).&#x20;

<figure><img src="../.gitbook/assets/image (7).png" alt="" width="375"><figcaption><p>As of Nov 14 2023, there are 149 staker nodes</p></figcaption></figure>

### What are the requirements for running a node?&#x20;

Running a node and staking requires technical competence and operational commitment. Stakers need to monitor their nodes closely to ensure correct node version, uptime, and prompt maintenance when needed.

### What is thhe tBTC Beta Stakers Program?&#x20;

As part of bootstrapping tBTC utility, Threshold Network allow-lists authorized stakers to participate in tBTC minting and redemptions to ensure system continuity during progressive decentralization. These allow-listed stakers are called Beta Stakers. The goal of the Beta Staker program is to ensure key operations run properly without interruptions during early growth.

As of Nov 14, 2023, there are 20 Beta Stakers across three staking providers (Boar, Staked, P2P) and self-hosted nodes. The number of Beta Stakers may change at any time. Visit [contract page on Etherscan](https://etherscan.io/address/0xc2731fb2823af3Efc2694c9bC86F444d5c5bb4Dc#readContract#F16) for the current figure.&#x20;

<figure><img src="../.gitbook/assets/image (8).png" alt="" width="563"><figcaption><p>As of Nov 14, 2023, there are 20 Beta Stakers</p></figcaption></figure>

For system requirements and cost estimates to run a staker node, please see [tbtc-beta-stakers-program.md](tbtc-beta-stakers-program.md "mention")



### How long will the Beta Stakers Program be in place?

Ultimately, the network's objective is to retire Beta Stakers Program altogether and make staking fully permissionless. For that, the following infrastructure upgrades have to take place:&#x20;

* Enable moving of funds across wallets&#x20;
* Decentralized Wallet Coordination ([RFC-12](https://github.com/keep-network/tbtc-v2/blob/6432ff0ff8c0bee046150a74f7e27eb06245f67f/docs/rfc/rfc-12.adoc))
* Optimistic redemptions (_Proposal pending_)&#x20;
* Signature scheme migration from ECDSA to Schnorr ([RFC-10](https://github.com/keep-network/tbtc-v2/blob/8822b31d74d7d9bf267b312aa54554e675f3fd79/docs/rfc/rfc-10.adoc#L4))
* Enable fraud and slashing mechanism in contracts in concert with automated fraud-defeat mechanism in Keep Core wallet client&#x20;
* Rewards calculation migrated to on-chain
