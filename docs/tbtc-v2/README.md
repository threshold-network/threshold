---
description: A Decentralized, Permissionless Bitcoin Bridge Onchain
---

# tBTC Bitcoin Bridge

Existing solutions that bridge Bitcoin to DeFi require users to send their Bitcoin to an intermediary, in exchange for an IOU token that represents the original asset. This centralized model requires you to trust a third party and is susceptible to censorship, threatening the premise of Bitcoin as sovereign, secure, permissionless digital asset.

In contrast, tBTC is a decentralized (and scalable) bridge between Bitcoin and DeFi. It provides Bitcoin holders secure and open access to the broader DeFi ecosystem, allowing you to unlock your Bitcoin’s value to borrow and lend, mint stablecoins, provide liquidity, and much more.

### Honest Majority Assumption

Instead of centralized intermediaries, tBTC uses a randomly selected group of operators running nodes on the Threshold Network to secure deposited Bitcoin through threshold cryptography. tBTC requires a threshold majority agreement before operators can perform any action with your Bitcoin. By rotating the selection of operators bi-weekly, tBTC protects against any individual or group of operators colluding to fraudulently seize the underlying deposits. By relying on an honest-majority-assumption, we can calculate the likelihood any wallet comprised of a quorum of dishonest operators (for a deeper discussion of the probability calculations, see [here](wallet-generation.md#statistics)).&#x20;

### Fees

tBTC fees are action-based (i.e., users incur a mint and redemption fee when using the bridge). The **mint and redemption fee is** [**0.2%**](https://etherscan.io/address/0x5e4861a80B55f035D899f66772117F00FA0E8e7B#readProxyContract#F13) **or 20 basis points**. The mint fee was initially offset to support adoption; however, following a recent governance decision, the mint fee was reinstated on April 15, 2026. You can read more about the reinstatement [here.](https://www.threshold.network/blog/tbtc-mint-fee-reinstatement)

### Fee Rebates or Fee Waivers

Minting and redemption fees are set at 20 basis points to support decentralized bridge operations. These fees can be partially or fully offset by staking $T. You can read the full announcement scope [here](https://www.threshold.network/blog/tbtc-redemption-fee-waivers-for-t-stakers).

**Staking $T is optional.** Eligible participants receive fee waivers in line with protocol parameters. Waivers are applied automatically when using the new Threshold Unified Bitcoin Router accessible at [https://app.threshold.network](https://app.threshold.network/).

The $T token is available on most decentralized exchanges and major centralized exchanges. To view an illustrative breakdown of how much $T is required to offset minting and redemption fees (assuming a $0.01 token price), refer to the diagram below.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-28 at 9.42.37 PM.png" alt=""><figcaption></figcaption></figure>

