---
description: Threshold Application – underlying technology
---

# Conditions-Based Decryption (CBD)

**TLDR**&#x20;

Conditions-Based Decryption (CBD) enables the users of adopting applications to specify conditions for accessing their encrypted data. If those conditions are not fulfilled – which means formal verification by a cohort of nodes from the Threshold network – then the data remain entirely unreadable to anyone besides the original data owner/encryptor. However, if the predefined conditions are provably satisfied, then the data requester gains decryption rights.

**Simplified Overview**&#x20;

Conditions-Based Decryption is a plug-in service that enables the sharing of any form of private or sensitive data within Web3 applications. Private data is encrypted by a data owner and remains encrypted until it reaches the device of a recipient. From a privacy point of view, this is comparable to the end-to-end encryption that a mobile messaging service like Signal offers.&#x20;

Whether or not a recipient qualifies to see a given piece of data depends on whether they are able to show that they fulfill certain conditions. These conditions are chosen in advance by the owner of that data, and often involve buying, receiving and holding a special-purpose NFT. Here are some real examples: \
\
(1) A graphic novelist wants to sell her work as a digital product, and include some bonus pages for those who pay a little more. She paywalls access to the main novel using NFT-A, and the bonus material with NFT-B. Those who buy the NFT-A are able to see the main part of the novel, and those who buy and hold both NFTs A & B are able to view the entire thing. Actually, anyone can download the novel in encrypted form, but without one of the two NFTs it can't be decrypted and is therefore useless. \
\
(2) A game developer wants to include a special map in their latest version for players who downloaded the original game in 2021, as a thanks for those who helped support the game studio in its early days. They encrypt the map build file and link it to two conditions; (i) that the player holds an NFT in their wallet that they would have received when they originally purchased the game, and (ii) that the player held this NFT before January 1st 2022. In this case, just holding the NFT is not enough to access the special map.&#x20;

In both these cases, the person (or device) trying to see the private data (the graphic novel and the game map) has to show to the Threshold network that they are supposed to have access. More specifically, they have to contact a group of Threshold nodes running CBD software, and prove to them that they hold the correct NFTs (and satisfy any other specified conditions, like having held them for a certain time).&#x20;

If a sufficient number of nodes are happy that the requesting person/device qualifies to see the data, they will send them some key fragments. These fragments can be put together by the requestor on their device, which produces a decrypting key. This decrypting key can be used to decrypt the original private data. &#x20;

**Scheme A: Threshold Decryption**

Under the hood, CBD involves splitting a joint secret – a decryption key – into multiples _shares_ and distributing those among authorized and collateralized node operators (stakers in the Threshold network). A minimum number – a _threshold_ – of those operators holding the key shares must be online and actively participate in partial decryptions. These are subsequently combined on the requester's client to reconstruct the original plaintext data.

**Scheme B: Distributed Key Generation**&#x20;

**Conditionality**

A range of access condition types can be defined by the data owner. For example:

* EVM-based\
  _e.g. Does the requester own a given NFT?_
* RPC-driven\
  _e.g. Does the requester have at least X amount of a given token in their wallet?_
* Time-based\
  _e.g. Has a predefined period elapsed, after which requests will be ignored?_

These conditions are also composable and can be combined in any logical sequence or decision tree.

Conditions are 'attached' on a per-ciphertext basis. In other words, each and every payload, message or bit can be access-restricted by a unique set of specified conditions. In most situations, condition sets will be automatically reused until the end-user proactively configures them – for example, in order to remove an address from continuing to access the messages in a group chat.

**Condition Fulfillment Verification**

Currently, requesters prove their association with condition fulfillment – i.e. their right to receive a threshold number of decrypting shares – by signing a transaction that verifies their ownership of a given Ethereum wallet. That wallet is checked for fulfillment of the specific condition – e.g. owning an NFT in order to access a DAO's knowledge base.

This signature can be cached by the application for a use case-appropriate period of time, such that the user does not have to repeatedly re-sign for access later. However, verification still takes place in the background – for example, if a wallet address is removed from an NFT-gated group-chat by dint of changing the access-granting NFT, they will immediately be unable to see new messages. The cached signature will not give them access any longer than is specified by the data owner/encryptor.

**Condition Update Authority**&#x20;

**Network & Cohort Parameterization**

In the future, developers will have the option to tweak certain network-level parameters, which affect the collusion-resistance, redundancy, latency and (in future versions) cost of using CBD. For example:

* The number of decrypting shares _n_
* The frequency and/or prompt for resampling a new cohort from the node array
* The 'hard-coded' node address that always feature in the cohort

This optionality can also be surfaced for end-users – for example, in the form of 'packages' that they might choose between, accommodating their risk and cost preferences . A guide on best practices for choosing/surfacing network parameters will be released soon.
