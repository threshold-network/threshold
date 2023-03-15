---
description: Trust-minimized Access Control
---

# Threshold Access Control

Threshold Access Control enables end-to-end encrypted data sharing and communication without the need to trust a centralized authority. It is the only access control layer available to Web3 developers that has **already achieved true decentralization** through a live and well-collateralized network, with sensitive cryptographic operations disassembled and distributed across \~250 independently operated servers.

End-users of applications which have integrated Threshold Access Control enjoy the following features & benefits:

* _End-to-end encryption for everything_.\
  Built on the privacy-for-everyone principles of popular end-to-end encrypted messengers but applicable to a far wider set of use cases, including private NFTs, connected vehicles, DBaaS, live-streaming, private DAO group chats, and much more.
* _Trust-minimization via cryptography and true decentralization._\
  Key management, condition verification and ciphertext re-encryption are operationally distributed across a geographically diverse array of machines/servers, operated by economically independent individuals and commercial entities.
* _Powerful, per-ciphertext conditionality._\
  Future access to data can be contingent on the fulfillment of nearly any predefined condition, and those conditions attached to any granularity of data payload (e.g. a single message or an entire table).
* _Flexible condition composability._\
  Conditions of all types can be mixed-and-matched using logical operators and flexible prefix notation into virtually any desired combination.
* _Tunable collusion-resistance, redundancy & latency._\
  Developers have full control over the cohort(s) of node operators which manage access to a given data payload, user base or entire application. These security parameters can also be packaged into simpler user-facing optionality for custom risk preferences.
* _Highly incentivized uptime._\
  Threshold Network's multi-app model strongly incentivizes node operators to provision service to tBTC, and its strict availability requirements. Threshold Access Control 'piggybacks' on tBTC uptime, reliability and technical competence.
* _Optional: keypair-only decryption._\
  If even stricter security guarantees are required, and data recipients' public keys are known in advance, developers may opt for end-user data to be re-encrypted by node operators such that they are only decryptable by pre-designated clients.

The Threshold Access Control service is built on two distinct but interwoven technologies; _Conditions-Based Decryption_ (CBD) and _Proxy Re-Encryption_ (PRE). Both offer trust-minimized end-to-end encryption and access control is executed by the same decentralized array of nodes. There are some trade-offs between (explicit) security and access condition customization, which are explored in the [trust assumptions](../../app-development/threshold-access-control-tac/trust-assumptions/) section.

Note for Threshold stakers: the up-and-running 'PRE app' will eventually be renamed to encompass the broader Threshold Access Control service, and will also acquire new functionality via the addition of CBD technology. This will require a DAO-driven upgrade but will not fundamentally change the operational requirements or compensation.
