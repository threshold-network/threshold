# Threshold DAO

Threshold is community-driven and governed by an eponymous DAO that includes the constituencies of both the NuCypher and Keep networks. The Threshold DAO has two primary bodies: the **Tokenholder DAO** and the **Elected Council**. The goal of this two-pronged structure is to enhance representation while ensuring accountability. Each of these governance bodies holds the other accountable, similar to the system of checks and balances found in most constitutional governments. They also hold separate responsibilities that are embedded in the governance structure.

![](<../../.gitbook/assets/Threshold DAO 4.png>)

From an implementation perspective, the Tokenholder DAO is based on the Governor Bravo governance model (in particular, using OpenZeppelin Governance). The Tokenholder DAO is on Ethereum Mainnet at `0xd101f2B25bCBF992BdF55dB67c104FE7646F5447`. The Elected Council is implemented as a Gnosis Safe contract, with a 6-of-9 configuration, also deployed on Ethereum Mainnet, at `0x9F6e831c8F8939DC0C830C6e492e7cEf4f9C2F5f`.

A technical architecture diagram is show below.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Threshold DAO Governance Architecture Diagram</p></figcaption></figure>
