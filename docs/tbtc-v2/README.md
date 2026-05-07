---
description: A Decentralized, Permissionless Bitcoin Bridge
---

# tBTC Bitcoin Bridge

Existing solutions that bridge Bitcoin to DeFi require users to send their Bitcoin to an intermediary in exchange for an IOU token representing the original asset. This centralized model requires you to trust a third party and is susceptible to censorship, threatening the premise of Bitcoin as a sovereign, secure, permissionless digital asset.

In contrast, tBTC is a decentralized and trust-minimized bridge between Bitcoin and DeFi. It provides Bitcoin holders with secure, open access to the broader DeFi ecosystem, allowing you to unlock the value of your Bitcoin by borrowing and lending, minting stablecoins, providing liquidity, and more.

{% hint style="info" %}
The New Unified Bitcoin Router by Threshold enables seamless tBTC minting, deminting, cross-chain bridging, and staking - all in one platform. It supports gasless minting and detects T staking rebates in real time. **Mint tBTC now at** [**https://app.threshold.network/**](https://app.threshold.network/)
{% endhint %}

### Honest Majority Assumption

Instead of centralized intermediaries, tBTC uses a randomly selected group of operators running nodes on the Threshold Network to secure deposited Bitcoin through [threshold cryptography](https://www.threshold.network/blog/what-is-threshold-cryptography). tBTC requires a threshold majority agreement before operators can perform any action with your Bitcoin.&#x20;

By rotating the selection of operators bi-weekly, tBTC protects against any individual or group of operators colluding to fraudulently seize the underlying deposits. By relying on an honest-majority assumption, we can calculate the likelihood of any wallet comprised of a quorum of dishonest operators.

<figure><img src="../.gitbook/assets/Threshold Cryptography.png" alt=""><figcaption></figcaption></figure>

### Fees

tBTC fees are action-based (i.e., users incur a mint and redemption fee when using the bridge). The **mint and redemption fee is** [**0.2%**](https://etherscan.io/address/0x5e4861a80B55f035D899f66772117F00FA0E8e7B#readProxyContract#F13) **or 20 basis points**. The mint fee was initially offset to support adoption; however, following a recent governance decision, it was reinstated on April 15, 2026. You can read more about the reinstatement [here.](https://www.threshold.network/blog/tbtc-mint-fee-reinstatement)

### Fee Rebates or Fee Waivers

Minting and redemption fees are set at 20 basis points to support decentralized bridge operations. However, these fees can be partially or fully offset by [staking $T](https://app.threshold.network/stake).&#x20;

**Staking $T is optional.** Eligible participants receive fee waivers in line with protocol parameters, and waivers are applied automatically when using the new Threshold Unified Bitcoin Router, accessible at [https://app.threshold.network](https://app.threshold.network).

{% hint style="info" %}
To learn more about T Token Staking and its benefits, **visit this** [**page**](../t-token/) **or read the** [**release article.**](https://www.threshold.network/blog/tbtc-redemption-fee-waivers-for-t-stakers)
{% endhint %}
