---
description: Trust-minimized Access Control
---

# Threshold Access Control

Threshold Access Control enables end-to-end encrypted data sharing and communication without the need to trust a centralized authority. It is the only access control layer available to Web3 developers that has **already achieved true decentralization** through a live and well-collateralized network, with sensitive cryptographic operations disassembled and distributed across \~150 independently operated nodes. Note that decentralization currently applies to the Proxy Re-Encryption service only, and the independence between nodes is observable, but not provable.&#x20;

The Threshold Access Control service is built on two distinct technologies; _Conditions-Based Decryption_ (CBD) and _Proxy Re-Encryption_ (PRE), both of which provide **end-to-end-encryption-as-a-service**. PRE access control is currently executed by a decentralized array of Threshold nodes, and the same array will execute CBD access control once it launches (see the [v.7.0.0](https://github.com/orgs/nucypher/projects/31/) roadmap).&#x20;

End-users of applications which have integrated Threshold Access Control enjoy the following features & benefits:

* **End-to-end encryption for everything**\
  Built on the privacy-for-everyone principles of popular end-to-end encrypted messengers but applicable to a far wider set of use cases, including private NFTs, connected vehicles, DBaaS, live-streaming, private DAO group chats, and much more.\
  _Note that most use cases are compatible with CBD, and a smaller subset with PRE._&#x20;
* **Trust-minimization via cryptography and true decentralization.**\
  Key management, condition verification and ciphertext re-encryption are operationally distributed across a geographically diverse array of machines/servers, operated by economically independent individuals and commercial entities.\
  _Note that only PRE is currently decentralized, permissionless and trust-minimized. However CBD's first version will launch running on the same node array and will be decentralized from day one._ &#x20;
* **Powerful, per-ciphertext conditionality.**\
  Future access to data can be contingent on the fulfillment of nearly any predefined condition, and those conditions attached to any granularity of data payload (e.g. a single message or an entire table).\
  _Applies only to CBD._
* **Flexible condition composability.**\
  Conditions of all types can be mixed-and-matched using logical operators and flexible prefix notation into virtually any desired combination. Conditions can also be flexibly surfaced at different stages of runtime. Conditions will also be updateable, even after being encrypted with the data payload. \
  _Applies only to CBD._
* **Tunable collusion-resistance, redundancy & latency.**\
  Developers have full control over the cohort(s) of node operators which manage access to a given data payload, user base or entire application. These security parameters can also be packaged into simpler user-facing optionality for custom risk preferences.\
  _Applies to future versions of CBD and potentially PRE too._&#x20;
* **Highly incentivized uptime.**\
  Threshold Network's multi-app model strongly incentivizes node operators to provision service to tBTC, and its strict availability requirements. Threshold Access Control 'piggybacks' on tBTC uptime, reliability and technical competence.\
  _Applies to both PRE & CBD (once launched)._&#x20;
* **Keypair-only decryption.**\
  If even stricter security guarantees are required, and data recipients' public keys are known in advance, developers may opt for end-user data to be re-encrypted by node operators such that they are only decryptable by pre-designated clients.\
  _Applies only to PRE._&#x20;

In choosing between PRE & CBD, there are some trade-offs between explicit security guarantees and access condition customization, which are explored in the [trust assumptions](../../app-development/threshold-access-control-tac/trust-assumptions/) section. Note that adopters can mix-and-math PRE & CBD within the same application for different sharing flows.&#x20;

_Note for Threshold stakers: the up-and-running 'PRE app' will eventually be renamed to encompass the broader Threshold Access Control service, and will also acquire new functionality via the addition of CBD technology. This will require a DAO-driven upgrade but early versions will not fundamentally change the operational requirements, although compensation may increase._&#x20;
