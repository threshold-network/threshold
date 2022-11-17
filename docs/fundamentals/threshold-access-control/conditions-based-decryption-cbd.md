---
description: Threshold Application – underlying technology
---

# Conditions-Based Decryption (CBD)

Conditions-Based Decryption (CBD) is one of two technologies underpinning the Threshold Access Control service, the other being Proxy Re-Encryption. The vast majority of developers will only require CBD technology to provably protect their users' data, and we recommend that all developers start here – by familiarizing themselves with CBD.&#x20;

In broad terms, CBD enables the users of adopting applications to specify conditions for accessing their encrypted data. If those conditions are not fulfilled – which means formal verification by a cohort of nodes from the Threshold network – then the data remain entirely unreadable to anyone besides the original data owner/encryptor. However, if the predefined conditions are provably satisfied, then the data requester gains decryption rights.&#x20;

**Threshold Decryption**

Under the hood, CBD involves splitting a joint secret – a decryption key – into multiples _shares_ and distributing those among authorized and collateralized node operators (stakers in the Threshold network). A minimum number – a _threshold_ – of those operators holding the key shares must be online and actively participate in partial decryptions. These are subsequently combined on the requester's client to reconstruct the original plaintext data.

**Conditionality**

A range of access condition types can be defined by the data owner. For example:&#x20;

* EVM-based \
  _e.g. Does the requester own a given NFT?_
* RPC-driven \
  _e.g. Does the requester have at least X amount of a given token in their wallet?_
* Time-based \
  _e.g. Has a predefined period elapsed, after which requests will be ignored?_

These conditions are also composable and can be combined in any logical sequence or decision tree.&#x20;

Conditions are 'attached' on a per-ciphertext basis. In other words, each and every payload, message or bit can theoretically can have accessed restricted by a unique set of specified conditions. In most situations, condition sets will be automatically reused until the end-user proactively configures them – for example, in order to remove an address from continuing to access the messages in a group chat.&#x20;

**Conditions Verification**

Currently, requesters prove their association with condition fulfillment – i.e. their right to receive a threshold number of decrypting shares – by signing a transaction that verifies their ownership of a given Ethereum wallet. That wallet is checked for fulfillment of the specific condition – e.g. owning an NFT in order to access a DAO's knowledge base.&#x20;

This signature can be cached by the application for a use case-appropriate period of time, such that the user does not have to repeatedly re-sign for access later. However, verification still takes place in the background – for example, if a wallet address is removed from an NFT-gated group-chat by dint of changing the access-granting NFT, they will immediately be unable to see new messages. The cached signature will not give them access any longer than is specified by the data owner/encryptor.

**Network Parameterization**&#x20;

CBD is fully decentralized. Therefore, developers have the option to tweak certain network parameters, which affect the collusion-resistance, redundancy, latency and (in future versions) cost of using CBD. For example:&#x20;

* The number of decrypting shares _n_&#x20;
* The frequency and/or prompt for resampling a new cohort from the node array
* The 'hard-coded' node address that always feature in the cohort

This optionality can also be surfaced for end-users – for example, in the form of 'packages' that they might choose between, accommodating their risk and cost preferences . A guide on best practices for choosing/surfacing network parameters will be released soon. &#x20;
