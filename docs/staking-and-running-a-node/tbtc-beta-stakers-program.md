# tBTC Beta Stakers Program

## About tBTC Beta Stakers Program

As part of bootstrapping tBTC utility, Threshold Network allow-lists authorized stakers to participate in tBTC minting and redemptions to ensure system continuity during progressive decentralization. These allow-listed stakers are called Beta Stakers. The goal with the Beta Staker program is to ensure key operations run properly without interruptions during early growth.

Note that this is a form of 'progressive decentralization', but with a clear, simple and already deployed _pathway_ for the decentralization depth to increase – i.e. by adding more independent stakers to the allow list.

As of the latest contract query, there are now 35 Beta Stakers, an increase from 20 as previously reported in November 2023. A few notable professional staking providers can be found requesting DAO approval [here](https://forum.threshold.network/t/tip-067-part-1-add-professional-node-operators-to-beta-staker-program/730/20).&#x20;

{% hint style="info" %}
Visit [contract page on Etherscan](https://etherscan.io/address/0xc2731fb2823af3Efc2694c9bC86F444d5c5bb4Dc#readContract#F16) to see the current number of Beta Stakers.&#x20;
{% endhint %}

Beta Staker nodes form a permissioned set that is responsible for creating tBTC wallets that custody BTC. Running a tBTC Beta Staker node is a significant commitment with specific requirements. This document aims to highlight such requirements, technical recommendations, and possible costs of operating a Beta Staker tBTC node.

To stay informed about current node activity and overall network health please visit [tbtcscan.com/operators](https://tbtcscan.com/operators) or check [status.threshold.network](https://status.threshold.network). These resources provide up-to-date statistics on node counts, stake distribution and operational metrics.

## General requirements

Running a Beta Staker node is a long-term commitment. Once the given node is added to the Beta Stakers set, it must remain operational until the end of the Beta Staker Program (\~12 months).

Operators of Beta Staker nodes are expected to be extremely responsive, especially regarding upgrades requested by software contributors. Ideally, they should be able to upgrade their nodes within 24 hours of notification.

Operators of Beta Staker nodes must be technically capable. They are responsible for ensuring high availability (more than 96% uptime) and security of the node.

## Technical requirements

A Beta Staker node performs more computationally expensive operations (DKG, threshold signing, etc) compared to a standard node. To ensure a high level of service, a Beta Staker node requires a machine with:

* 4 CPUs
* 4 GB of RAM
* 1 GB of persistent disk space
* 80 Mbps of network bandwidth
* Linux OS

In addition to the above:&#x20;

* The underlying machine’s operating system and tools must be up to date, especially regarding recent security patches.
* Backups of the persistent disk must be performed on an ongoing basis. This is crucial to ensure the safety of mission-critical data (e.g. key material). The usage of an additional encryption layer for backups is highly recommended.
* The network layer must provide a unique public IPv4 address allowing the node to be reached from the external network. A proper firewall configuration must be in place and ensure a safe perimeter allowing inbound traffic on two ports (communication and diagnostics). The internet connection must be stable and failover scenarios must be taken into account.
* A Beta Staker node requires access to an Ethereum node. It is highly recommended to set up a private Ethereum stack (e.g. Geth + Prysm) and use public providers as failover (e.g. Alchemy, Infura, etc). Such a setup is crucial for network diversity and decentralization as it helps avoid the single-point-of-failure risk associated with public providers. Beta Staker operators should use their own judgement for selecting stack components. Specific technical requirements for a private Ethereum stack depend on the software used. For example, a stack based on [Geth + Prysm requires 4 CPU / 16 GB RAM / 2 TB SSD](https://docs.prylabs.network/docs/install/install-with-script#step-1-review-prerequisites-and-best-practices).
* A Beta Staker node requires an Electrum protocol server to interact with Bitcoin. It is highly recommended to set up a private stack consisting of an Electrum server (e.g. Fulcrum, ElectrumX, Electrs, etc) paired with a Bitcoin node (e.g. Bitcoin Core) and use public servers as failover. The reasons are exactly the same as for a private Ethereum stack. Beta Staker operators should use their own judgement for selecting stack components. Specific technical requirements for a private Bitcoin stack depend on the software used. For example, a stack based on Fulcrum + Bitcoin Core requires 8 CPU / 16 GB RAM / 2 TB SSD.
* It is highly recommended to set up a private monitoring stack to ensure observability and alerting. The monitoring system should supervise the node itself as well as Ethereum and Bitcoin stacks. Beta Staker operators should use their own judgement for selecting stack components. Specific technical requirements for a monitoring stack depend on the software used. For example, a stack based on Grafana + Prometheus requires 2 CPU / 4 GB RAM / 20 GB HDD.

## Cost estimation

The actual monthly costs of running a Beta Staker node depends on the hosting model (VPS vs self-hosting) and infrastructure configuration. The following estimation aims to provide a ballpark figure. It also assumes that all aforementioned technical requirements and recommendations are taken into account.&#x20;

**Key system assumptions**

* Beta Staker node requires 4 CPUs / 4 GB RAM / 1 GB HDD
* Ethereum stack requires 4 CPUs / 16 GB RAM / 2 TB SSD
* Bitcoin stack requires 8 CPUs / 16 GB RAM / 2 TB SSD
* Monitoring stack requires 2 CPUs / 4 GB RAM / 20 GB HDD
* External storage for backups (node and auxiliary stacks) is 1 TB HDD
* Network layer ensures 100 Mbps of bandwidth and includes all necessary equipment (firewall, public IPs, etc)

The above assumptions can be used to divide costs into 4 categories:

1. **Computing costs:** This category includes the costs of running 18 CPUs and 40 GB RAM in total. There are a number of possible configurations to provide such computing capacity. Using the VPS-based model as an example, the cost of 3x 8CPU/16GB VMs on leading cloud service providers starts from \~$400/month.
2. **Storage costs:** This category includes the costs of using 4 TB SSD and \~1 TB HDD of storage in total. For the VPS-based model, part of this cost is often included in the cost of VMs, but some providers charge for the storage separately with rates like \~$0.08/GB (e.g., Amazon EBS). In that case, it is safe to assume that storage costs start from \~$200/month.
3. **Network costs:** This category includes the cost of 100 Mbps of bandwidth and all auxiliary equipment and features like a firewall or public IPs. For the VPS-based model, such bandwidth is often provided out of the box with the VMs, but a firewall and public IPs may be charged separately. Moreover, some providers may apply an additional charge on egress traffic. It is safe to assume that network costs start from \~$50/month.
4. **Other costs:** This category includes all maintenance and other costs depending on the hosting model and infrastructure configuration. For the VPS-based model, such costs can be OS licenses, automatic backup, additional management tools, and so on. For the self-hosting model, these can be costs of internet connection, power failover (e.g., UPS), auxiliary network equipment, and similar. This category of costs must be factored into the total monthly cost.

## Bottom Line

In summary, the cost for running a Beta Staker node using the VPS-based model starts at approximately $650/month. Assuming a safety margin for other costs, a rough cost estimate is $800/month for running a setup in accordance with the technical requirements and recommendations.&#x20;

Estimating the general cost for the self-hosting model is non-trivial due to the variety of possible configurations. The total monthly cost can be similar to the VPS-based model but may also incur additional hidden costs such as hardware maintenance

## **Disclaimer**

Above assumptions and figures should be used cautiously as it is only intended to provide a general sense of the infrastructure costs. Each prospective Beta Staker node operator should conduct their own estimates based on their individual circumstances.&#x20;

Last but not least, the presented estimate refers only to the infrastructure-related costs of operating a tBTC node. This estimate DOES NOT include human operational costs. Each Beta Staker operator must include this overhead when making their own estimates.\
