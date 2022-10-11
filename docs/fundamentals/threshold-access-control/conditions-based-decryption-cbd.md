---
description: Threshold Application – underlying technology
---

# Conditions-Based Decryption (CBD)

Condition-Based Decryption (CBD) is the primary technology underpinning Threshold Access Control with which most adopting developers will interact. For Web3 use cases involving private data, the vast majority require a means of accessing that data that that is based on the fulfillment of conditions by a given data requester. &#x20;

Condition-Based Decryption (CBD) is a programmable access control service, in which decryption rights are based on the verified fulfillment of predefined conditions. Access conditions can be EVM-based (e.g. does the requester own this NFT?), RPC-driven (e.g. does the requester have at least X amount of a given token in their wallet?) or time-based (e.g. has a preset period elapsed, after which the recipient's requests will be ignored?). These conditions are also composable and can be combined in any logical sequence or decision tree.

CBD involves splitting a joint secret (a decryption key) into multiples _shares_ and distributing those among authorized and collateralized node operators ([Stakers](https://threshold.network/earn/staker) in the Threshold network). A minimum number – a _threshold_ – of those operators holding the key shares must be online and actively participate in partial decryptions that can subsequently be combined by the requester to reconstruct the original plaintext data.

