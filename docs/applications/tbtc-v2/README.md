---
description: A Decentralized, Permissionless Bitcoin Bridge to DeFi
---

# tBTC Bitcoin Bridge

{% embed url="https://youtu.be/9jnDtNQ_GEc" %}
tBTC intro video
{% endembed %}

Existing solutions that bridge Bitcoin to Ethereum require users to send their Bitcoin to an intermediary, in exchange for an ERC-20 token that represents the original asset. This centralized model requires you to trust a third party and is susceptible to censorship, threatening the premise of Bitcoin as sovereign, secure, permissionless digital asset.

The second generation of tBTC **** is a truly decentralized (and scalable) bridge between Bitcoin and Ethereum. It provides Bitcoin holders secure and open access to the broader DeFi ecosystem. tBTC v2 allows you to unlock your Bitcoin’s value to borrow and lend, mint stablecoins, provide liquidity, and much more.

Instead of centralized intermediaries, tBTC uses a randomly selected group of operators running nodes on the Threshold Network to secure deposited Bitcoin through threshold cryptography. That means tBTC requires a threshold majority agreement before operators perform any action with your Bitcoin. By rotating the selection of operators weekly, tBTC protects against any individual or group of operators colluding to fraudulently seize the underlying deposits. By relying on an honest-majoriy-assumption, we can calculate the likelihood any wallet comprised of a quorum of dishonest operators (for a deeper discussion of the probability calculations, see [here](wallet-generation.md#statistics). An insurance backstop (the coverage pools), serves as an emergency fallback in the event a wallet is compromised.

Unlike other solutions on the market, users of tBTC trust math, not hardware or people.
